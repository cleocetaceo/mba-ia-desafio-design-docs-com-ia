# PRD: Sistema de Webhooks de Notificação de Pedidos

| Campo | Valor |
|---|---|
| Responsável de produto | Marcos (Product Manager) |
| Tech Lead | Larissa |
| Status | Aprovado em reunião; implementação a iniciar |
| Data | 2026-11-17 |
| Origem | Reunião técnica de quinta-feira, 09:00 ([TRANSCRICAO.md](../TRANSCRICAO.md)) |
| Documentos | [RFC](RFC.md) · [FDD](FDD.md) · [ADRs](adrs/) · [Tracker](TRACKER.md) |

## 1. Resumo e contexto da feature

O Order Management System (OMS) passará a avisar os clientes B2B, por webhook, sempre que o status de um pedido deles mudar. O cliente cadastra uma URL `https`, escolhe de quais status quer ser avisado e passa a receber uma requisição assinada a cada mudança, sem precisar consultar a API repetidamente.

A demanda veio de um pedido formal de três clientes, Atlas Comercial, MaxDistribuição e Nova Cargo ([09:00] Marcos). Hoje o OMS não tem nenhum mecanismo de notificação externa.

## 2. Problema e motivação

- **Integração lenta e cara.** Para saber se um pedido mudou, os clientes fazem polling periódico em `GET /orders` ([09:00] Marcos).
- **Risco comercial.** A Atlas sinalizou que pode migrar para um concorrente se a solução não sair até o fim do trimestre ([09:00] Marcos), e pediu a entrega para o fim de novembro ([09:45] Marcos).
- **Expectativa de tempo real.** Para esses clientes, qualquer atraso abaixo de 10 segundos é tempo real; o essencial é não precisar atualizar manualmente ([09:02] Marcos).

## 3. Público-alvo e cenários de uso

**Públicos**

| Público | Necessidade |
|---|---|
| Times de integração dos clientes B2B (Atlas, MaxDistribuição, Nova Cargo) | Receber mudanças de status sem polling, validar a origem das requisições e não processar duplicatas |
| Usuários do OMS que representam o cliente | Cadastrar e manter os webhooks pela API do OMS com JWT ([09:32] Marcos) |
| Administradores do OMS | Reprocessar entregas que falharam de forma permanente ([09:36] Sofia) |
| Produto (Marcos) | Documentar a integração no portal do desenvolvedor ([09:26] Marcos, [09:40] Marcos) |

**Cenários**

1. **Assinar só o que interessa.** A Atlas cadastra um webhook para `SHIPPED` e `DELIVERED`. Quando um pedido dela é enviado, ela recebe a notificação em poucos segundos ([09:33] Marcos).
2. **Manutenção planejada.** O endpoint da MaxDistribuição fica fora do ar por duas horas. As notificações do período chegam depois, por retentativas automáticas ([09:16] Diego).
3. **Secret vazada.** A Nova Cargo descobre que a secret apareceu num log. Ela pede uma nova pela API e tem 24 horas para atualizar os sistemas, sem perder notificações ([09:21] Sofia, [09:22] Diego).
4. **Notificação duplicada.** O cliente recebe o mesmo evento duas vezes e descarta a cópia pelo identificador do evento ([09:25] Diego).
5. **Falha prolongada.** O endpoint fica indisponível por mais de 15 horas. O evento vai para uma fila de falhas, e um administrador o reprocessa quando o cliente volta ([09:18] Diego).
6. **Investigação.** O cliente consulta o histórico das últimas entregas para entender por que não processou um pedido ([09:34] Marcos).

## 4. Objetivos e métricas de sucesso

| ID | Objetivo | Métrica | Meta |
|---|---|---|---|
| `[PRD-OBJ-01]` | Notificar em tempo real | Tempo entre a mudança de status e a entrega bem-sucedida, com o cliente disponível | **p95 abaixo de 10 segundos** ([09:02] Marcos) |
| `[PRD-OBJ-02]` | Atender os clientes que fizeram o pedido | Clientes B2B com webhook ativo em produção | **3 clientes** (Atlas, MaxDistribuição, Nova Cargo) ([09:00] Marcos) |
| `[PRD-OBJ-03]` | Cumprir o prazo da Atlas | Sprints até o deploy, com a revisão de segurança | **3 sprints**, para o fim de novembro ([09:45] Marcos, [09:47] Larissa) |
| `[PRD-OBJ-04]` | Não perder notificações | Mudanças de status com webhook elegível que não geraram evento | **0** ([09:40] Bruno) |

O volume de polling em `GET /orders` pelos três clientes é acompanhado como indicador da redução de custo de integração. A reunião não definiu linha de base nem meta para ele.

## 5. Escopo

### 5.1 Incluso

- Cadastro, listagem, edição e remoção de webhooks por cliente, com filtro de status.
- Notificação a cada mudança de status de pedido.
- Assinatura verificável das notificações e rotação da secret.
- Retentativas automáticas, fila de falhas permanentes e reprocessamento por administrador.
- Histórico de entregas por webhook.

### 5.2 Fora de escopo

| ID | Item | Situação | Origem |
|---|---|---|---|
| `[PRD-OOS-01]` | E-mail ao cliente quando o webhook falha repetidamente | Adiado para a próxima fase, depois de medir o impacto | [09:37] Marcos, [09:37] Larissa |
| `[PRD-OOS-02]` | Dashboard ou painel visual de webhooks para o cliente | Descartado nesta fase; é projeto separado do time de frontend | [09:39] Marcos, [09:40] Larissa |
| `[PRD-OOS-03]` | Rate limiting de envio por cliente | Adiado: observar e decidir depois | [09:38] Diego, [09:39] Larissa |
| `[PRD-OOS-04]` | Webhooks de entrada (cliente enviando para a plataforma) | Descartado: os clientes só querem receber | [09:02] Marcos |
| `[PRD-OOS-05]` | Garantia de ordem global entre pedidos | Descartado; os clientes não pediram | [09:13] Larissa, [09:14] Marcos |
| `[PRD-OOS-06]` | Entrega exatamente uma vez (exactly-once) | Descartado | [09:25] Diego |
| `[PRD-OOS-07]` | Arquivamento de notificações antigas já entregues | Fora desta feature | [09:08] Diego |
| `[PRD-OOS-08]` | Itens do pedido na notificação | Descartado; o cliente consulta o pedido se precisar | [09:43] Diego |

## 6. Requisitos funcionais

| ID | Requisito | Origem |
|---|---|---|
| `[PRD-FR-01]` | O cliente cadastra um webhook informando URL e lista de status de interesse. A plataforma gera a secret e a devolve na criação. | [09:31] Marcos |
| `[PRD-FR-02]` | O cadastro é feito pela API do OMS, autenticado com JWT, e o cliente é indicado na requisição, não no token. | [09:32] Marcos, [09:32] Larissa |
| `[PRD-FR-03]` | É possível listar os webhooks de um cliente. | [09:33] Bruno |
| `[PRD-FR-04]` | É possível editar um webhook (URL, status de interesse e ativação). | [09:33] Bruno, [09:21] Bruno |
| `[PRD-FR-05]` | É possível remover um webhook. | [09:33] Bruno |
| `[PRD-FR-06]` | Cada webhook recebe apenas os status escolhidos; se nenhum webhook do cliente quer aquele status, nada é gerado. | [09:33] Marcos, [09:34] Bruno |
| `[PRD-FR-07]` | Toda mudança de status de um pedido gera uma notificação para os webhooks elegíveis do cliente dono do pedido. As notificações só saem da plataforma para o cliente. | [09:00] Marcos, [09:02] Marcos |
| `[PRD-FR-08]` | A notificação traz identificador do evento, tipo, data e hora, pedido, número do pedido, status anterior e novo, cliente e valor total. | [09:43] Diego |
| `[PRD-FR-09]` | A notificação traz uma assinatura que permite ao cliente confirmar a origem e a integridade. | [09:19] Sofia, [09:20] Sofia |
| `[PRD-FR-10]` | O cliente pode pedir uma nova secret pela API; a anterior continua válida por 24 horas. | [09:21] Sofia |
| `[PRD-FR-11]` | Cada notificação traz um identificador único do evento, que se repete em reenvios, para o cliente deduplicar. | [09:25] Diego |
| `[PRD-FR-12]` | Cada notificação informa qual cadastro de webhook a originou. | [09:44] Sofia |
| `[PRD-FR-13]` | Quando o envio falha, a plataforma tenta de novo automaticamente, com intervalos crescentes, por até cerca de 15 horas. | [09:15] Diego, [09:17] Diego |
| `[PRD-FR-14]` | Esgotadas as tentativas, a notificação vai para uma fila de falhas permanentes. | [09:15] Diego, [09:18] Diego |
| `[PRD-FR-15]` | Um administrador pode reprocessar uma notificação da fila de falhas; a ação exige perfil `ADMIN` e registra quem a executou. | [09:18] Diego, [09:36] Sofia |
| `[PRD-FR-16]` | O cliente consulta o histórico das últimas 100 entregas de um webhook, com sucesso ou falha, conteúdo enviado, resposta e tempo de resposta. | [09:34] Marcos |

## 7. Requisitos não funcionais

| ID | Requisito | Origem |
|---|---|---|
| `[PRD-NFR-01]` | Latência abaixo de 10 segundos no caminho feliz; a leitura de eventos acontece a cada 2 segundos. | [09:02] Marcos, [09:10] Larissa |
| `[PRD-NFR-02]` | A mudança de status nunca depende da disponibilidade do cliente, e não existe mudança de status sem o evento correspondente. | [09:04] Bruno, [09:40] Bruno |
| `[PRD-NFR-03]` | A URL do webhook precisa ser `https`; cadastro com `http` é recusado. | [09:23] Sofia |
| `[PRD-NFR-04]` | Notificações acima de 64 KB não são enviadas e são tratadas como erro. | [09:23] Sofia, [09:24] Larissa |
| `[PRD-NFR-05]` | Cada envio espera no máximo 10 segundos pela resposta do cliente. | [09:42] Diego |
| `[PRD-NFR-06]` | Cada webhook tem secret própria; não existe secret global. | [09:21] Sofia |
| `[PRD-NFR-07]` | Entrega pelo menos uma vez (at-least-once); duplicatas são possíveis e documentadas. | [09:24] Diego, [09:26] Marcos |
| `[PRD-NFR-08]` | Ordem garantida apenas por pedido. | [09:13] Larissa |
| `[PRD-NFR-09]` | Sem infraestrutura nova: mesmo banco e mesma stack, com os padrões do projeto. | [09:07] Diego, [09:30] Larissa |
| `[PRD-NFR-10]` | O processamento das notificações continua funcionando quando a API reinicia. | [09:11] Diego |
| `[PRD-NFR-11]` | O código de segurança passa por revisão de pelo menos dois dias úteis antes do deploy. | [09:46] Sofia |

## 8. Decisões e trade-offs principais

| Decisão | Trade-off aceito | Registro |
|---|---|---|
| Registrar o evento numa outbox no MySQL, na mesma transação da mudança de status | Banco usado como fila, em troca de consistência e de nenhuma infraestrutura nova | [ADR-001](adrs/ADR-001-outbox-no-mysql.md) |
| Worker separado lendo a cada 2 segundos | Até 2 s de atraso e um único worker, em troca de simplicidade e ordem por pedido | [ADR-002](adrs/ADR-002-worker-em-processo-separado.md) |
| 5 retentativas em cerca de 15 horas, depois fila de falhas | Atraso possível de até cerca de 15 h e reprocessamento manual | [ADR-003](adrs/ADR-003-retry-backoff-e-dlq.md) |
| Assinatura HMAC-SHA256 com secret por webhook e rotação de 24 h | Uma secret para gerenciar por endpoint | [ADR-004](adrs/ADR-004-hmac-sha256-secret-por-endpoint.md) |
| Entrega at-least-once com identificador de evento | O cliente precisa deduplicar | [ADR-005](adrs/ADR-005-at-least-once-com-x-event-id.md) |
| Reuso dos padrões do projeto | Herda os limites atuais, como permissões amplas no CRUD | [ADR-006](adrs/ADR-006-reuso-dos-padroes-do-projeto.md) |
| Conteúdo da notificação congelado no momento da mudança | Mais espaço no banco | [ADR-007](adrs/ADR-007-snapshot-de-payload-na-insercao.md) |

## 9. Dependências

| ID | Dependência | Origem |
|---|---|---|
| `[PRD-DEP-01]` | Revisão de segurança da Sofia, com dois dias úteis reservados antes do deploy | [09:46] Sofia, [09:49] Sofia |
| `[PRD-DEP-02]` | Documentação da integração no portal do desenvolvedor, com destaque para duplicatas | [09:26] Marcos, [09:40] Marcos |
| `[PRD-DEP-03]` | Deploy de um segundo processo (worker) junto da API | [09:11] Diego |
| `[PRD-DEP-04]` | Clientes com endpoint `https`, verificação de assinatura e deduplicação do lado deles | [09:23] Sofia, [09:25] Diego |
| `[PRD-DEP-05]` | Confirmação do prazo com a Atlas | [09:47] Marcos |

## 10. Riscos e mitigação

| ID | Risco | Probabilidade | Impacto | Mitigação | Origem |
|---|---|---|---|---|---|
| `[PRD-RISCO-01]` | Atraso além do fim de novembro leva a Atlas ao concorrente | Média | Alto | Escopo enxuto com itens adiados, estimativa de 3 sprints e prazo confirmado com o cliente | [09:00] Marcos, [09:46] Larissa |
| `[PRD-RISCO-02]` | Endpoint do cliente indisponível por horas | Alta | Médio | Retentativas por cerca de 15 h e fila de falhas com reprocessamento | [09:16] Diego |
| `[PRD-RISCO-03]` | Cliente vaza a secret | Média | Alto | Secret por webhook e rotação com 24 h de transição | [09:21] Sofia, [09:22] Diego |
| `[PRD-RISCO-04]` | Cliente processa a mesma notificação duas vezes | Média | Médio | Identificador do evento e documentação destacada no portal | [09:25] Sofia, [09:26] Marcos |
| `[PRD-RISCO-05]` | Rajada de notificações sobrecarrega um cliente | Média | Médio | Acompanhar a volumetria e decidir sobre rate limiting depois | [09:38] Diego, [09:39] Larissa |
| `[PRD-RISCO-06]` | Revisão de segurança empurra o deploy | Média | Médio | Revisão incluída nas 3 sprints | [09:46] Sofia, [09:47] Larissa |

## 11. Critérios de aceitação

| ID | Critério |
|---|---|
| `[PRD-CA-01]` | Um cliente com webhook ativo para `SHIPPED` recebe a notificação em menos de 10 segundos quando um pedido dele é enviado. |
| `[PRD-CA-02]` | Um cliente não recebe notificação de status que não assinou. |
| `[PRD-CA-03]` | O cadastro com URL `http` é recusado com erro de validação. |
| `[PRD-CA-04]` | A secret aparece apenas na resposta de criação e de rotação. |
| `[PRD-CA-05]` | O cliente consegue validar a assinatura de cada notificação com a secret que recebeu, inclusive durante as 24 h de rotação, com qualquer uma das duas secrets. |
| `[PRD-CA-06]` | Com o endpoint fora do ar por duas horas, as notificações do período chegam depois que ele volta. |
| `[PRD-CA-07]` | Uma notificação que falha em todas as tentativas aparece na fila de falhas e só um administrador consegue reprocessá-la; quem reprocessou fica registrado. |
| `[PRD-CA-08]` | O histórico mostra as últimas 100 entregas com resultado, conteúdo, resposta e tempo de resposta. |
| `[PRD-CA-09]` | Reenvios do mesmo evento chegam com o mesmo identificador. |
| `[PRD-CA-10]` | A mudança de status de um pedido funciona normalmente mesmo com o endpoint do cliente fora do ar. |

Os critérios técnicos detalhados estão no [FDD, seção 13](FDD.md#13-critérios-de-aceite-técnicos).

## 12. Estratégia de testes e validação

- **Testes de integração** com Vitest e Supertest, no padrão de `tests/orders.test.ts`: CRUD de webhooks, validação de `https`, permissão `ADMIN` no reprocessamento e geração do evento dentro da transação de mudança de status.
- **Testes do worker** contra um servidor HTTP local de teste que responde sucesso, erro ou demora: assinatura, headers, timeout, sequência de retentativas (com relógio simulado) e ida para a fila de falhas.
- **Teste de atomicidade:** falha forçada na gravação do evento precisa desfazer a mudança de status.
- **Regressão:** a suíte existente em `tests/` continua passando.
- **Revisão de segurança** da Sofia sobre HMAC e geração de secret antes do deploy ([09:46] Sofia).
- **Validação com os clientes:** Marcos confirma prazo e publica a documentação no portal ([09:47] Marcos, [09:40] Marcos); a meta de latência é medida pelos logs do worker depois do deploy.
