# Tracker de rastreabilidade

Cada linha liga um item do pacote documental à origem na transcrição ([TRANSCRICAO.md](../TRANSCRICAO.md)) ou no código do repositório.

Convenções:

- **ID**: os IDs de PRD, RFC e FDD aparecem entre colchetes nos próprios documentos (por exemplo `[PRD-FR-01]`). Nos ADRs, `ADR-NNN` é a decisão, e `ADR-NNN-ALT-0X` é a alternativa de número X da seção "Alternativas Consideradas".
- **Localização**: para `TRANSCRICAO`, `[hh:mm] Nome` do falante. Quando a fala completa se divide entre falantes, a linha cita a fala que fecha o item. Para `CODIGO`, o caminho do arquivo a partir da raiz do repositório.
- Quando a fala define o comportamento, mas não o nome técnico (por exemplo, o path da rotação de secret), o conteúdo da linha indica o que foi derivado.

| ID | Documento | Tipo | Conteúdo (resumo) | Fonte | Localização |
|---|---|---|---|---|---|
| PRD-CTX-01 | docs/PRD.md | Contexto | Pedido formal de Atlas Comercial, MaxDistribuição e Nova Cargo por notificação de status | TRANSCRICAO | [09:00] Marcos |
| PRD-CTX-02 | docs/PRD.md | Restrição | O OMS só tem módulos REST (auth, users, customers, products, orders), sem eventos ou filas | CODIGO | src/app.ts |
| PRD-PROB-01 | docs/PRD.md | Problema | Clientes fazem polling em GET /orders; Atlas ameaça migrar até o fim do trimestre | TRANSCRICAO | [09:00] Marcos |
| PRD-PROB-02 | docs/PRD.md | Problema | "Tempo real" significa menos de 10 segundos para os clientes | TRANSCRICAO | [09:02] Marcos |
| PRD-PUB-01 | docs/PRD.md | Público | Usuários do OMS que representam o cliente cadastram pela API com JWT | TRANSCRICAO | [09:32] Marcos |
| PRD-PUB-02 | docs/PRD.md | Público | Administradores reprocessam a fila de falhas | TRANSCRICAO | [09:36] Sofia |
| PRD-CEN-01 | docs/PRD.md | Cenário | Assinar apenas SHIPPED e DELIVERED | TRANSCRICAO | [09:33] Marcos |
| PRD-CEN-02 | docs/PRD.md | Cenário | Manutenção planejada de duas horas no cliente | TRANSCRICAO | [09:16] Diego |
| PRD-CEN-03 | docs/PRD.md | Cenário | Secret vazada em log do cliente e rotação | TRANSCRICAO | [09:22] Diego |
| PRD-CEN-04 | docs/PRD.md | Cenário | Notificação duplicada descartada pelo identificador do evento | TRANSCRICAO | [09:25] Diego |
| PRD-CEN-05 | docs/PRD.md | Cenário | Falha prolongada vai para a fila de falhas e é reprocessada | TRANSCRICAO | [09:18] Diego |
| PRD-CEN-06 | docs/PRD.md | Cenário | Cliente investiga pelo histórico de entregas | TRANSCRICAO | [09:34] Marcos |
| PRD-OBJ-01 | docs/PRD.md | Métrica | p95 da entrega abaixo de 10 segundos | TRANSCRICAO | [09:02] Marcos |
| PRD-OBJ-02 | docs/PRD.md | Métrica | 3 clientes B2B com webhook ativo | TRANSCRICAO | [09:00] Marcos |
| PRD-OBJ-03 | docs/PRD.md | Métrica | Entrega em 3 sprints, incluindo revisão de segurança | TRANSCRICAO | [09:47] Larissa |
| PRD-OBJ-04 | docs/PRD.md | Métrica | Zero mudanças de status elegíveis sem evento | TRANSCRICAO | [09:40] Bruno |
| PRD-OOS-01 | docs/PRD.md | Fora de escopo | E-mail ao cliente em falhas repetidas, adiado para a próxima fase | TRANSCRICAO | [09:37] Larissa |
| PRD-OOS-02 | docs/PRD.md | Fora de escopo | Dashboard visual; projeto separado do frontend | TRANSCRICAO | [09:40] Larissa |
| PRD-OOS-03 | docs/PRD.md | Fora de escopo | Rate limiting de saída: observar e decidir depois | TRANSCRICAO | [09:39] Larissa |
| PRD-OOS-04 | docs/PRD.md | Fora de escopo | Webhooks de entrada | TRANSCRICAO | [09:02] Marcos |
| PRD-OOS-05 | docs/PRD.md | Fora de escopo | Ordem global entre pedidos | TRANSCRICAO | [09:14] Marcos |
| PRD-OOS-06 | docs/PRD.md | Fora de escopo | Exactly-once | TRANSCRICAO | [09:25] Diego |
| PRD-OOS-07 | docs/PRD.md | Fora de escopo | Arquivamento das notificações entregues | TRANSCRICAO | [09:08] Diego |
| PRD-OOS-08 | docs/PRD.md | Fora de escopo | Itens do pedido no payload | TRANSCRICAO | [09:43] Diego |
| PRD-FR-01 | docs/PRD.md | Requisito Funcional | Cadastrar webhook com URL e status; secret gerada e devolvida na criação | TRANSCRICAO | [09:31] Marcos |
| PRD-FR-02 | docs/PRD.md | Requisito Funcional | Cadastro pela API com JWT; customer_id na requisição, não no token | TRANSCRICAO | [09:32] Larissa |
| PRD-FR-03 | docs/PRD.md | Requisito Funcional | Listar webhooks de um cliente | TRANSCRICAO | [09:33] Bruno |
| PRD-FR-04 | docs/PRD.md | Requisito Funcional | Editar webhook (URL, status, ativo) | TRANSCRICAO | [09:33] Bruno |
| PRD-FR-05 | docs/PRD.md | Requisito Funcional | Remover webhook | TRANSCRICAO | [09:33] Bruno |
| PRD-FR-06 | docs/PRD.md | Requisito Funcional | Filtro de status por webhook, aplicado na inserção | TRANSCRICAO | [09:34] Bruno |
| PRD-FR-07 | docs/PRD.md | Requisito Funcional | Notificar toda mudança de status, só outbound | TRANSCRICAO | [09:02] Marcos |
| PRD-FR-08 | docs/PRD.md | Requisito Funcional | Campos do payload: event_id, tipo, timestamp, pedido, status, cliente, total | TRANSCRICAO | [09:43] Diego |
| PRD-FR-09 | docs/PRD.md | Requisito Funcional | Assinatura para o cliente validar origem e integridade | TRANSCRICAO | [09:20] Sofia |
| PRD-FR-10 | docs/PRD.md | Requisito Funcional | Rotação de secret pela API com 24 h de validade da anterior | TRANSCRICAO | [09:21] Sofia |
| PRD-FR-11 | docs/PRD.md | Requisito Funcional | Identificador único do evento para deduplicação | TRANSCRICAO | [09:25] Diego |
| PRD-FR-12 | docs/PRD.md | Requisito Funcional | Identificação do cadastro de origem (X-Webhook-Id) | TRANSCRICAO | [09:44] Sofia |
| PRD-FR-13 | docs/PRD.md | Requisito Funcional | Retentativas automáticas com intervalos crescentes por cerca de 15 h | TRANSCRICAO | [09:17] Diego |
| PRD-FR-14 | docs/PRD.md | Requisito Funcional | Fila de falhas permanentes (DLQ) | TRANSCRICAO | [09:18] Diego |
| PRD-FR-15 | docs/PRD.md | Requisito Funcional | Reprocessamento por ADMIN com registro de quem executou | TRANSCRICAO | [09:36] Sofia |
| PRD-FR-16 | docs/PRD.md | Requisito Funcional | Histórico das últimas 100 entregas com resultado, payload, resposta e tempo | TRANSCRICAO | [09:34] Marcos |
| PRD-NFR-01 | docs/PRD.md | Requisito Não Funcional | Latência abaixo de 10 s; polling de 2 s | TRANSCRICAO | [09:10] Larissa |
| PRD-NFR-02 | docs/PRD.md | Requisito Não Funcional | Mudança de status independente do cliente e atômica com o evento | TRANSCRICAO | [09:40] Bruno |
| PRD-NFR-03 | docs/PRD.md | Requisito Não Funcional | URL obrigatoriamente https | TRANSCRICAO | [09:23] Sofia |
| PRD-NFR-04 | docs/PRD.md | Requisito Não Funcional | Teto de 64 KB, com erro | TRANSCRICAO | [09:24] Larissa |
| PRD-NFR-05 | docs/PRD.md | Requisito Não Funcional | Timeout de 10 s por envio | TRANSCRICAO | [09:42] Diego |
| PRD-NFR-06 | docs/PRD.md | Requisito Não Funcional | Secret por webhook, sem secret global | TRANSCRICAO | [09:21] Sofia |
| PRD-NFR-07 | docs/PRD.md | Requisito Não Funcional | At-least-once documentado | TRANSCRICAO | [09:24] Diego |
| PRD-NFR-08 | docs/PRD.md | Requisito Não Funcional | Ordem garantida só por pedido | TRANSCRICAO | [09:13] Larissa |
| PRD-NFR-09 | docs/PRD.md | Requisito Não Funcional | Sem infraestrutura nova, padrões do projeto | TRANSCRICAO | [09:30] Larissa |
| PRD-NFR-10 | docs/PRD.md | Requisito Não Funcional | Processamento sobrevive a restart da API | TRANSCRICAO | [09:11] Diego |
| PRD-NFR-11 | docs/PRD.md | Requisito Não Funcional | Revisão de segurança de dois dias úteis antes do deploy | TRANSCRICAO | [09:46] Sofia |
| PRD-DEP-01 | docs/PRD.md | Dependência | Revisão de segurança da Sofia | TRANSCRICAO | [09:49] Sofia |
| PRD-DEP-02 | docs/PRD.md | Dependência | Documentação no portal do desenvolvedor | TRANSCRICAO | [09:26] Marcos |
| PRD-DEP-03 | docs/PRD.md | Dependência | Deploy de um segundo processo (worker) | TRANSCRICAO | [09:11] Diego |
| PRD-DEP-04 | docs/PRD.md | Dependência | Cliente com https, verificação HMAC e deduplicação | TRANSCRICAO | [09:25] Diego |
| PRD-DEP-05 | docs/PRD.md | Dependência | Confirmação do prazo com a Atlas | TRANSCRICAO | [09:47] Marcos |
| PRD-RISCO-01 | docs/PRD.md | Risco | Atraso leva a Atlas ao concorrente | TRANSCRICAO | [09:00] Marcos |
| PRD-RISCO-02 | docs/PRD.md | Risco | Endpoint do cliente indisponível por horas | TRANSCRICAO | [09:16] Diego |
| PRD-RISCO-03 | docs/PRD.md | Risco | Vazamento de secret pelo cliente | TRANSCRICAO | [09:22] Diego |
| PRD-RISCO-04 | docs/PRD.md | Risco | Cliente processa duplicata | TRANSCRICAO | [09:25] Sofia |
| PRD-RISCO-05 | docs/PRD.md | Risco | Rajada de notificações para um cliente | TRANSCRICAO | [09:38] Diego |
| PRD-RISCO-06 | docs/PRD.md | Risco | Revisão de segurança empurra o deploy | TRANSCRICAO | [09:46] Sofia |
| PRD-CA-01 | docs/PRD.md | Critério de aceitação | Notificação de SHIPPED em menos de 10 s | TRANSCRICAO | [09:33] Marcos |
| PRD-CA-02 | docs/PRD.md | Critério de aceitação | Status não assinado não é notificado | TRANSCRICAO | [09:34] Bruno |
| PRD-CA-03 | docs/PRD.md | Critério de aceitação | URL http recusada | TRANSCRICAO | [09:23] Sofia |
| PRD-CA-04 | docs/PRD.md | Critério de aceitação | Secret só na criação e na rotação | TRANSCRICAO | [09:31] Marcos |
| PRD-CA-05 | docs/PRD.md | Critério de aceitação | Assinatura válida com qualquer das duas secrets durante a rotação | TRANSCRICAO | [09:21] Sofia |
| PRD-CA-06 | docs/PRD.md | Critério de aceitação | Notificações chegam depois de duas horas de indisponibilidade | TRANSCRICAO | [09:16] Diego |
| PRD-CA-07 | docs/PRD.md | Critério de aceitação | Fila de falhas reprocessável só por ADMIN, com registro | TRANSCRICAO | [09:36] Sofia |
| PRD-CA-08 | docs/PRD.md | Critério de aceitação | Histórico com as últimas 100 entregas | TRANSCRICAO | [09:34] Marcos |
| PRD-CA-09 | docs/PRD.md | Critério de aceitação | Reenvio mantém o identificador do evento | TRANSCRICAO | [09:25] Diego |
| PRD-CA-10 | docs/PRD.md | Critério de aceitação | Mudança de status funciona com o cliente fora do ar | TRANSCRICAO | [09:04] Bruno |
| PRD-TEST-01 | docs/PRD.md | Estratégia de testes | Testes de integração com Vitest e Supertest no padrão existente | CODIGO | tests/orders.test.ts |
| PRD-TEST-02 | docs/PRD.md | Estratégia de testes | Revisão de segurança sobre HMAC e geração de secret | TRANSCRICAO | [09:46] Sofia |
| RFC-CTX-01 | docs/RFC.md | Contexto | Demanda dos três clientes e risco de churn da Atlas | TRANSCRICAO | [09:00] Marcos |
| RFC-CTX-02 | docs/RFC.md | Contexto | Transação de changeStatus atualiza pedido, histórico e estoque | CODIGO | src/modules/orders/order.service.ts |
| RFC-PROP-01 | docs/RFC.md | Decisão | Captura transacional com publishWebhookEvent e filtro na inserção | TRANSCRICAO | [09:41] Bruno |
| RFC-PROP-02 | docs/RFC.md | Decisão | Um worker em processo próprio, ordem por pedido | TRANSCRICAO | [09:12] Diego |
| RFC-PROP-03 | docs/RFC.md | Decisão | Timeout de 10 s, backoff, DLQ e replay ADMIN | TRANSCRICAO | [09:48] Larissa |
| RFC-PROP-04 | docs/RFC.md | Decisão | https, HMAC-SHA256 com secret por endpoint e rotação de 24 h | TRANSCRICAO | [09:22] Sofia |
| RFC-PROP-05 | docs/RFC.md | Decisão | At-least-once com X-Event-Id | TRANSCRICAO | [09:26] Larissa |
| RFC-PROP-06 | docs/RFC.md | Decisão | Superfície de API no módulo src/modules/webhooks com os padrões do projeto | TRANSCRICAO | [09:30] Larissa |
| RFC-ALT-01 | docs/RFC.md | Trade-off | HTTP síncrono descartado: cliente lento trava mudanças de status | TRANSCRICAO | [09:04] Bruno |
| RFC-ALT-02 | docs/RFC.md | Trade-off | Redis Streams descartado: infraestrutura nova, overengineering | TRANSCRICAO | [09:07] Diego |
| RFC-ALT-03 | docs/RFC.md | Trade-off | Trigger MySQL descartada: não notifica processo externo | TRANSCRICAO | [09:09] Diego |
| RFC-ALT-04 | docs/RFC.md | Trade-off | 3 retentativas ou retry indefinido descartados | TRANSCRICAO | [09:16] Diego |
| RFC-ALT-05 | docs/RFC.md | Trade-off | Secret global descartada: um vazamento expõe todos | TRANSCRICAO | [09:21] Sofia |
| RFC-ALT-06 | docs/RFC.md | Trade-off | Exactly-once descartado: coordenação dos dois lados | TRANSCRICAO | [09:25] Diego |
| RFC-ALT-07 | docs/RFC.md | Trade-off | Renderização no envio descartada: evento perderia fidelidade | TRANSCRICAO | [09:52] Larissa |
| RFC-OPEN-01 | docs/RFC.md | Questão em aberto | Rate limiting de envio: observar e decidir depois | TRANSCRICAO | [09:39] Larissa |
| RFC-OPEN-02 | docs/RFC.md | Questão em aberto | Endurecer permissões do CRUD de webhooks | TRANSCRICAO | [09:37] Sofia |
| RFC-OPEN-03 | docs/RFC.md | Questão em aberto | Escala para vários workers (particionamento ou lock) | TRANSCRICAO | [09:13] Diego |
| RFC-OPEN-04 | docs/RFC.md | Questão em aberto | Aviso por e-mail ao cliente em falhas repetidas | TRANSCRICAO | [09:37] Larissa |
| RFC-OPEN-05 | docs/RFC.md | Questão em aberto | Arquivamento das linhas entregues (cerca de 30 dias) | TRANSCRICAO | [09:08] Diego |
| RFC-IMP-01 | docs/RFC.md | Impacto | changeStatus falha se a inserção na outbox falhar | TRANSCRICAO | [09:40] Bruno |
| RFC-IMP-02 | docs/RFC.md | Impacto | Nova entry-point executada como processo próprio | TRANSCRICAO | [09:11] Larissa |
| RFC-RISCO-01 | docs/RFC.md | Risco | Cliente indisponível por horas | TRANSCRICAO | [09:16] Diego |
| RFC-RISCO-02 | docs/RFC.md | Risco | Vazamento de secret pelo cliente | TRANSCRICAO | [09:22] Diego |
| RFC-RISCO-03 | docs/RFC.md | Risco | Evento duplicado no cliente | TRANSCRICAO | [09:26] Marcos |
| RFC-RISCO-04 | docs/RFC.md | Risco | Crescimento da outbox | TRANSCRICAO | [09:07] Bruno |
| RFC-RISCO-05 | docs/RFC.md | Risco | Rajada de eventos para um cliente | TRANSCRICAO | [09:38] Diego |
| RFC-RISCO-06 | docs/RFC.md | Risco | Prazo da Atlas e estimativa de três sprints | TRANSCRICAO | [09:46] Larissa |
| ADR-001 | docs/adrs/ADR-001-outbox-no-mysql.md | Decisão | Outbox no MySQL na mesma transação da mudança de status | TRANSCRICAO | [09:08] Larissa |
| ADR-001-CTX | docs/adrs/ADR-001-outbox-no-mysql.md | Contexto | changeStatus usa prisma.$transaction com update, history e estoque | CODIGO | src/modules/orders/order.service.ts |
| ADR-001-DB | docs/adrs/ADR-001-outbox-no-mysql.md | Restrição | Banco existente é MySQL via Prisma | CODIGO | prisma/schema.prisma |
| ADR-001-IDX | docs/adrs/ADR-001-outbox-no-mysql.md | Decisão | Índices em status e created_at | TRANSCRICAO | [09:08] Diego |
| ADR-001-UUID | docs/adrs/ADR-001-outbox-no-mysql.md | Decisão | Chave UUID na outbox, padrão do projeto | TRANSCRICAO | [09:51] Larissa |
| ADR-001-ALT-01 | docs/adrs/ADR-001-outbox-no-mysql.md | Trade-off | HTTP síncrono em changeStatus descartado | TRANSCRICAO | [09:06] Diego |
| ADR-001-ALT-02 | docs/adrs/ADR-001-outbox-no-mysql.md | Trade-off | Redis Streams descartado | TRANSCRICAO | [09:07] Larissa |
| ADR-002 | docs/adrs/ADR-002-worker-em-processo-separado.md | Decisão | Worker em polling de 2 s, latência mínima aceita | TRANSCRICAO | [09:10] Larissa |
| ADR-002-PROC | docs/adrs/ADR-002-worker-em-processo-separado.md | Decisão | Worker fora do processo da API | TRANSCRICAO | [09:11] Diego |
| ADR-002-ENTRY | docs/adrs/ADR-002-worker-em-processo-separado.md | Decisão | src/worker.ts e npm run worker, no modelo de src/server.ts | TRANSCRICAO | [09:11] Larissa |
| ADR-002-SERVER | docs/adrs/ADR-002-worker-em-processo-separado.md | Restrição | Entry-point atual da API usada como modelo | CODIGO | src/server.ts |
| ADR-002-PRISMA | docs/adrs/ADR-002-worker-em-processo-separado.md | Decisão | PrismaClient próprio, mesma DATABASE_URL | TRANSCRICAO | [09:30] Bruno |
| ADR-002-ORD | docs/adrs/ADR-002-worker-em-processo-separado.md | Restrição | Ordem só por order_id e com um único worker | TRANSCRICAO | [09:13] Larissa |
| ADR-002-ALT-01 | docs/adrs/ADR-002-worker-em-processo-separado.md | Trade-off | Trigger do MySQL descartada | TRANSCRICAO | [09:09] Diego |
| ADR-002-ALT-02 | docs/adrs/ADR-002-worker-em-processo-separado.md | Trade-off | Worker no mesmo processo da API descartado | TRANSCRICAO | [09:11] Diego |
| ADR-002-ALT-03 | docs/adrs/ADR-002-worker-em-processo-separado.md | Trade-off | Vários workers adiados | TRANSCRICAO | [09:13] Diego |
| ADR-003 | docs/adrs/ADR-003-retry-backoff-e-dlq.md | Decisão | 5 retentativas com backoff 1m/5m/30m/2h/12h | TRANSCRICAO | [09:17] Larissa |
| ADR-003-CONT | docs/adrs/ADR-003-retry-backoff-e-dlq.md | Decisão | Envio inicial mais 5 retentativas, quase 15 h até a última | TRANSCRICAO | [09:17] Diego |
| ADR-003-DLQ | docs/adrs/ADR-003-retry-backoff-e-dlq.md | Decisão | DLQ em tabela webhook_dead_letter | TRANSCRICAO | [09:18] Diego |
| ADR-003-REPLAY | docs/adrs/ADR-003-retry-backoff-e-dlq.md | Decisão | Replay manual via endpoint admin | TRANSCRICAO | [09:18] Diego |
| ADR-003-ALT-01 | docs/adrs/ADR-003-retry-backoff-e-dlq.md | Trade-off | 3 retentativas descartadas | TRANSCRICAO | [09:16] Diego |
| ADR-003-ALT-02 | docs/adrs/ADR-003-retry-backoff-e-dlq.md | Trade-off | Retry indefinido descartado | TRANSCRICAO | [09:15] Diego |
| ADR-003-ALT-03 | docs/adrs/ADR-003-retry-backoff-e-dlq.md | Trade-off | Flag failed na própria outbox descartada | TRANSCRICAO | [09:18] Diego |
| ADR-003-ALT-04 | docs/adrs/ADR-003-retry-backoff-e-dlq.md | Trade-off | E-mail ao cliente fora desta fase | TRANSCRICAO | [09:37] Larissa |
| ADR-004 | docs/adrs/ADR-004-hmac-sha256-secret-por-endpoint.md | Decisão | HMAC-SHA256 do corpo, secret por endpoint, rotação de 24 h | TRANSCRICAO | [09:22] Sofia |
| ADR-004-OUT | docs/adrs/ADR-004-hmac-sha256-secret-por-endpoint.md | Restrição | Webhook só outbound | TRANSCRICAO | [09:03] Sofia |
| ADR-004-CFG | docs/adrs/ADR-004-hmac-sha256-secret-por-endpoint.md | Decisão | Configuração com URL, secret, customer_id e estado ativo | TRANSCRICAO | [09:21] Bruno |
| ADR-004-HTTPS | docs/adrs/ADR-004-hmac-sha256-secret-por-endpoint.md | Restrição | URL https validada em schema Zod | TRANSCRICAO | [09:23] Sofia |
| ADR-004-ALT-01 | docs/adrs/ADR-004-hmac-sha256-secret-por-endpoint.md | Trade-off | Secret global descartada | TRANSCRICAO | [09:21] Sofia |
| ADR-004-ALT-02 | docs/adrs/ADR-004-hmac-sha256-secret-por-endpoint.md | Trade-off | Corte imediato da secret antiga descartado | TRANSCRICAO | [09:21] Sofia |
| ADR-005 | docs/adrs/ADR-005-at-least-once-com-x-event-id.md | Decisão | At-least-once com X-Event-Id | TRANSCRICAO | [09:26] Larissa |
| ADR-005-ID | docs/adrs/ADR-005-at-least-once-com-x-event-id.md | Decisão | UUID gerado na entrada da outbox, único por evento | TRANSCRICAO | [09:25] Diego |
| ADR-005-PORTAL | docs/adrs/ADR-005-at-least-once-com-x-event-id.md | Dependência | Documentação destacada no portal | TRANSCRICAO | [09:26] Marcos |
| ADR-005-ALT-01 | docs/adrs/ADR-005-at-least-once-com-x-event-id.md | Trade-off | Exactly-once descartado | TRANSCRICAO | [09:25] Diego |
| ADR-005-CONS | docs/adrs/ADR-005-at-least-once-com-x-event-id.md | Trade-off | Responsabilidade de deduplicar fica com o cliente | TRANSCRICAO | [09:25] Sofia |
| ADR-006 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Decisão | Reuso máximo dos padrões existentes | TRANSCRICAO | [09:30] Larissa |
| ADR-006-MOD | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Decisão | Módulo src/modules/webhooks com a estrutura padrão | TRANSCRICAO | [09:27] Bruno |
| ADR-006-WRK | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Decisão | Processamento em webhook.worker.ts dentro do módulo | TRANSCRICAO | [09:28] Bruno |
| ADR-006-ERR | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Decisão | Erros AppError com prefixo WEBHOOK_ | TRANSCRICAO | [09:29] Larissa |
| ADR-006-PINO | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Decisão | Pino e middleware de erro sem mudança | TRANSCRICAO | [09:29] Bruno |
| ADR-006-ROLE | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Decisão | requireRole existente no replay | TRANSCRICAO | [09:36] Larissa |
| ADR-006-FN | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Decisão | publishWebhookEvent(tx, order, fromStatus, toStatus) | TRANSCRICAO | [09:41] Bruno |
| ADR-006-ALT-02 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Trade-off | Injetar o repository no OrderService descartado | TRANSCRICAO | [09:41] Diego |
| ADR-006-ALT-03 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Restrição | PrismaClient não é compartilhável entre processos | TRANSCRICAO | [09:30] Bruno |
| ADR-006-C01 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Padrão do código | Classe AppError com statusCode e errorCode | CODIGO | src/shared/errors/app-error.ts |
| ADR-006-C02 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Padrão do código | InvalidStatusTransitionError e InsufficientStockError | CODIGO | src/shared/errors/http-errors.ts |
| ADR-006-C03 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Padrão do código | Middleware trata AppError, ZodError e Prisma P2002/P2025 | CODIGO | src/middlewares/error.middleware.ts |
| ADR-006-C04 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Padrão do código | authenticate, requireRole e roles ADMIN/OPERATOR | CODIGO | src/middlewares/auth.middleware.ts |
| ADR-006-C05 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Padrão do código | Logger Pino com redact | CODIGO | src/shared/logger/index.ts |
| ADR-006-C06 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Padrão do código | Validação Zod via validate | CODIGO | src/middlewares/validate.middleware.ts |
| ADR-006-C07 | docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md | Padrão do código | Registro de routers em buildApiRouter | CODIGO | src/routes/index.ts |
| ADR-007 | docs/adrs/ADR-007-snapshot-de-payload-na-insercao.md | Decisão | Payload renderizado na inserção | TRANSCRICAO | [09:52] Larissa |
| ADR-007-Q | docs/adrs/ADR-007-snapshot-de-payload-na-insercao.md | Contexto | Pergunta: payload pronto ou só order_id | TRANSCRICAO | [09:51] Bruno |
| ADR-007-DEL | docs/adrs/ADR-007-snapshot-de-payload-na-insercao.md | Contexto | Pedidos podem ser excluídos em PENDING e CANCELLED | CODIGO | src/modules/orders/order.service.ts |
| ADR-007-ALT-01 | docs/adrs/ADR-007-snapshot-de-payload-na-insercao.md | Trade-off | Renderizar no envio descartado | TRANSCRICAO | [09:52] Diego |
| FDD-CTX-01 | docs/FDD.md | Contexto | Sequência da transação de changeStatus | CODIGO | src/modules/orders/order.service.ts |
| FDD-CTX-02 | docs/FDD.md | Contexto | canTransition, shouldDebitStock e shouldReplenishStock | CODIGO | src/modules/orders/order.status.ts |
| FDD-OBJ-01 | docs/FDD.md | Objetivo técnico | Evento atômico com a mudança de status | TRANSCRICAO | [09:40] Bruno |
| FDD-OBJ-02 | docs/FDD.md | Objetivo técnico | Entrega abaixo de 10 s | TRANSCRICAO | [09:02] Marcos |
| FDD-OBJ-03 | docs/FDD.md | Objetivo técnico | Backoff e DLQ | TRANSCRICAO | [09:17] Larissa |
| FDD-OBJ-04 | docs/FDD.md | Objetivo técnico | Verificação por HMAC-SHA256 | TRANSCRICAO | [09:22] Sofia |
| FDD-OBJ-05 | docs/FDD.md | Objetivo técnico | Padrões do projeto, sem dependências novas | TRANSCRICAO | [09:30] Larissa |
| FDD-EXC-01 | docs/FDD.md | Fora de escopo | Webhooks de entrada | TRANSCRICAO | [09:02] Marcos |
| FDD-EXC-02 | docs/FDD.md | Fora de escopo | E-mail em falhas repetidas | TRANSCRICAO | [09:37] Larissa |
| FDD-EXC-03 | docs/FDD.md | Fora de escopo | Rate limiting de saída | TRANSCRICAO | [09:39] Larissa |
| FDD-EXC-04 | docs/FDD.md | Fora de escopo | Dashboard visual | TRANSCRICAO | [09:40] Larissa |
| FDD-EXC-05 | docs/FDD.md | Fora de escopo | Arquivamento após cerca de 30 dias | TRANSCRICAO | [09:08] Diego |
| FDD-EXC-06 | docs/FDD.md | Fora de escopo | Vários workers e ordem global | TRANSCRICAO | [09:13] Larissa |
| FDD-EXC-07 | docs/FDD.md | Fora de escopo | Itens do pedido no payload | TRANSCRICAO | [09:43] Diego |
| FDD-EXC-08 | docs/FDD.md | Fora de escopo | Evento na criação do pedido (só changeStatus publica) | TRANSCRICAO | [09:40] Bruno |
| FDD-DADOS-01 | docs/FDD.md | Modelo de dados | webhook_endpoints com URL, secret, customer_id e ativo | TRANSCRICAO | [09:21] Bruno |
| FDD-DADOS-02 | docs/FDD.md | Modelo de dados | Índices de status e created_at na outbox | TRANSCRICAO | [09:08] Diego |
| FDD-DADOS-03 | docs/FDD.md | Modelo de dados | Status PENDING, PROCESSING, FAILED e DELIVERED | TRANSCRICAO | [09:08] Diego |
| FDD-DADOS-04 | docs/FDD.md | Modelo de dados | id da outbox é o event_id (UUID na inserção) | TRANSCRICAO | [09:25] Diego |
| FDD-DADOS-05 | docs/FDD.md | Modelo de dados | webhook_dead_letter com payload, motivo e timestamp | TRANSCRICAO | [09:18] Diego |
| FDD-DADOS-06 | docs/FDD.md | Modelo de dados | webhook_deliveries alimenta o histórico | TRANSCRICAO | [09:34] Marcos |
| FDD-DADOS-07 | docs/FDD.md | Modelo de dados | Outbox sem FK para orders por ser snapshot | TRANSCRICAO | [09:52] Larissa |
| FDD-DADOS-08 | docs/FDD.md | Padrão do código | Ids uuid() Char(36) em todos os models | CODIGO | prisma/schema.prisma |
| FDD-FLUXO-01 | docs/FDD.md | Fluxo | publishWebhookEvent com o tx, depois do history | TRANSCRICAO | [09:41] Bruno |
| FDD-FLUXO-02 | docs/FDD.md | Fluxo | Filtro por endpoint ativo e status, na inserção | TRANSCRICAO | [09:34] Bruno |
| FDD-FLUXO-03 | docs/FDD.md | Fluxo | Uma linha por endpoint, com payload renderizado | TRANSCRICAO | [09:52] Larissa |
| FDD-FLUXO-04 | docs/FDD.md | Fluxo | Falha na outbox causa rollback da mudança | TRANSCRICAO | [09:40] Bruno |
| FDD-FLUXO-05 | docs/FDD.md | Fluxo | create não publica evento | TRANSCRICAO | [09:40] Bruno |
| FDD-FLUXO-06 | docs/FDD.md | Fluxo | PROCESSING volta para PENDING na inicialização (aceito pelo at-least-once) | TRANSCRICAO | [09:24] Diego |
| FDD-FLUXO-07 | docs/FDD.md | Fluxo | Ciclo de 2 s, eventos mais antigos em lote pequeno | TRANSCRICAO | [09:09] Diego |
| FDD-FLUXO-08 | docs/FDD.md | Fluxo | Evento não entregue segura os seguintes do mesmo pedido (ordem por order_id) | TRANSCRICAO | [09:12] Diego |
| FDD-FLUXO-09 | docs/FDD.md | Fluxo | Serialização única e checagem de tamanho | TRANSCRICAO | [09:24] Larissa |
| FDD-FLUXO-10 | docs/FDD.md | Fluxo | Acima de 64 KB não envia nem trunca; vai à DLQ | TRANSCRICAO | [09:23] Sofia |
| FDD-FLUXO-11 | docs/FDD.md | Fluxo | fetch com timeout de 10 s | TRANSCRICAO | [09:42] Diego |
| FDD-FLUXO-12 | docs/FDD.md | Fluxo | 2xx marca como entregue | TRANSCRICAO | [09:08] Diego |
| FDD-FLUXO-13 | docs/FDD.md | Fluxo | Fora de 2xx, timeout ou erro de rede é falha | TRANSCRICAO | [09:42] Diego |
| FDD-FLUXO-14 | docs/FDD.md | Fluxo | Encerramento gracioso como no servidor HTTP | CODIGO | src/server.ts |
| FDD-RETRY-01 | docs/FDD.md | Regra | Cálculo de nextAttemptAt e passagem para a DLQ após o 6º envio | TRANSCRICAO | [09:17] Larissa |
| FDD-RETRY-02 | docs/FDD.md | Regra | Janela de 14 h 36 min até a última retentativa | TRANSCRICAO | [09:17] Diego |
| FDD-RETRY-03 | docs/FDD.md | Regra | Mesmo payload e X-Event-Id em retentativas | TRANSCRICAO | [09:25] Diego |
| FDD-DLQ-01 | docs/FDD.md | Fluxo | Upsert na DLQ e remoção da outbox | TRANSCRICAO | [09:18] Diego |
| FDD-DLQ-02 | docs/FDD.md | Fluxo | Replay com requireRole('ADMIN') | TRANSCRICAO | [09:36] Larissa |
| FDD-DLQ-03 | docs/FDD.md | Fluxo | Replay recoloca como pendente com o mesmo event_id | TRANSCRICAO | [09:18] Diego |
| FDD-DLQ-04 | docs/FDD.md | Fluxo | Log de auditoria do replay com userId | TRANSCRICAO | [09:36] Sofia |
| FDD-DLQ-05 | docs/FDD.md | Regra | Item já reprocessado não é reprocessado de novo (preserva a evidência) | TRANSCRICAO | [09:18] Diego |
| FDD-ROT-01 | docs/FDD.md | Fluxo | Nova secret e anterior válida por 24 h | TRANSCRICAO | [09:21] Sofia |
| FDD-ROT-02 | docs/FDD.md | Fluxo | Dupla assinatura durante a janela | TRANSCRICAO | [09:21] Sofia |
| FDD-ROT-03 | docs/FDD.md | Regra | Rotação dupla invalida a secret mais antiga | TRANSCRICAO | [09:21] Sofia |
| FDD-ROT-04 | docs/FDD.md | Fluxo | Log de rotação sem o valor da secret | TRANSCRICAO | [09:22] Diego |
| FDD-CONTRATO-01 | docs/FDD.md | Contrato | Payload JSON enxuto order.status_changed | TRANSCRICAO | [09:43] Diego |
| FDD-CONTRATO-02 | docs/FDD.md | Contrato | HMAC-SHA256 hex sobre o corpo enviado | TRANSCRICAO | [09:20] Sofia |
| FDD-CONTRATO-03 | docs/FDD.md | Contrato | Headers Content-Type, X-Event-Id, X-Signature, X-Timestamp e X-Webhook-Id | TRANSCRICAO | [09:44] Diego |
| FDD-CONTRATO-04 | docs/FDD.md | Contrato | 2xx em até 10 s é entrega; sem seguir redirecionamentos (só https) | TRANSCRICAO | [09:42] Diego |
| FDD-CONTRATO-05 | docs/FDD.md | Contrato | POST /api/v1/webhooks | TRANSCRICAO | [09:31] Marcos |
| FDD-CONTRATO-06 | docs/FDD.md | Contrato | GET /api/v1/webhooks?customerId | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-07 | docs/FDD.md | Contrato | PATCH /api/v1/webhooks/:id | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-08 | docs/FDD.md | Contrato | DELETE /api/v1/webhooks/:id | TRANSCRICAO | [09:33] Bruno |
| FDD-CONTRATO-09 | docs/FDD.md | Contrato | POST /api/v1/webhooks/:id/rotate-secret (comportamento da reunião, path derivado) | TRANSCRICAO | [09:21] Sofia |
| FDD-CONTRATO-10 | docs/FDD.md | Contrato | GET /api/v1/webhooks/:id/deliveries com as últimas 100 | TRANSCRICAO | [09:34] Marcos |
| FDD-CONTRATO-11 | docs/FDD.md | Contrato | POST /api/v1/admin/webhooks/dead-letter/:id/replay | TRANSCRICAO | [09:35] Diego |
| FDD-CONTRATO-12 | docs/FDD.md | Padrão do código | Prefixo /api/v1 aplicado em buildApp | CODIGO | src/app.ts |
| FDD-CONTRATO-13 | docs/FDD.md | Padrão do código | Formato de erro { error: { code, message, details } } | CODIGO | src/middlewares/error.middleware.ts |
| FDD-CONTRATO-14 | docs/FDD.md | Restrição | customerId no corpo ou na query, não no JWT | TRANSCRICAO | [09:32] Larissa |
| FDD-ERR-01 | docs/FDD.md | Erro | WEBHOOK_NOT_FOUND | TRANSCRICAO | [09:28] Bruno |
| FDD-ERR-02 | docs/FDD.md | Erro | WEBHOOK_INVALID_URL para URL sem https | TRANSCRICAO | [09:23] Sofia |
| FDD-ERR-03 | docs/FDD.md | Erro | WEBHOOK_INVALID_EVENTS para lista vazia | TRANSCRICAO | [09:33] Marcos |
| FDD-ERR-04 | docs/FDD.md | Erro | WEBHOOK_DEAD_LETTER_NOT_FOUND | TRANSCRICAO | [09:35] Diego |
| FDD-ERR-05 | docs/FDD.md | Erro | WEBHOOK_DEAD_LETTER_ALREADY_REPLAYED | TRANSCRICAO | [09:18] Diego |
| FDD-ERR-06 | docs/FDD.md | Erro | WEBHOOK_DELIVERY_TIMEOUT após 10 s | TRANSCRICAO | [09:42] Diego |
| FDD-ERR-07 | docs/FDD.md | Erro | WEBHOOK_DELIVERY_FAILED para resposta fora de 2xx ou erro de rede | TRANSCRICAO | [09:15] Diego |
| FDD-ERR-08 | docs/FDD.md | Erro | WEBHOOK_PAYLOAD_TOO_LARGE acima de 64 KB | TRANSCRICAO | [09:24] Larissa |
| FDD-ERR-09 | docs/FDD.md | Padrão do código | Reuso de VALIDATION_ERROR, UNAUTHORIZED, FORBIDDEN e NOT_FOUND | CODIGO | src/shared/errors/http-errors.ts |
| FDD-ERR-10 | docs/FDD.md | Restrição | WEBHOOK_SECRET_REQUIRED não se aplica: secret gerada pela plataforma | TRANSCRICAO | [09:31] Marcos |
| FDD-RES-01 | docs/FDD.md | Resiliência | Timeout de 10 s | TRANSCRICAO | [09:42] Diego |
| FDD-RES-02 | docs/FDD.md | Resiliência | 5 retentativas | TRANSCRICAO | [09:17] Larissa |
| FDD-RES-03 | docs/FDD.md | Resiliência | Backoff 1m/5m/30m/2h/12h | TRANSCRICAO | [09:17] Diego |
| FDD-RES-04 | docs/FDD.md | Resiliência | Fallback em DLQ com replay ADMIN | TRANSCRICAO | [09:18] Diego |
| FDD-RES-05 | docs/FDD.md | Resiliência | Nenhum HTTP dentro de changeStatus | TRANSCRICAO | [09:04] Bruno |
| FDD-RES-06 | docs/FDD.md | Resiliência | Recuperação de PROCESSING após queda | TRANSCRICAO | [09:24] Diego |
| FDD-RES-07 | docs/FDD.md | Resiliência | API e worker independentes | TRANSCRICAO | [09:11] Diego |
| FDD-RES-08 | docs/FDD.md | Resiliência | Limite de 64 KB sem truncar | TRANSCRICAO | [09:24] Diego |
| FDD-RES-09 | docs/FDD.md | Resiliência | Ordem por pedido preservada nas retentativas | TRANSCRICAO | [09:13] Larissa |
| FDD-OBS-01 | docs/FDD.md | Observabilidade | Logs de início e parada do worker no Pino | TRANSCRICAO | [09:29] Bruno |
| FDD-OBS-02 | docs/FDD.md | Observabilidade | Log webhook_delivery_succeeded com latência | TRANSCRICAO | [09:29] Bruno |
| FDD-OBS-03 | docs/FDD.md | Observabilidade | Log webhook_delivery_failed com errorCode | TRANSCRICAO | [09:29] Bruno |
| FDD-OBS-04 | docs/FDD.md | Observabilidade | Log webhook_dead_lettered | TRANSCRICAO | [09:18] Diego |
| FDD-OBS-05 | docs/FDD.md | Observabilidade | Log de auditoria do replay | TRANSCRICAO | [09:36] Sofia |
| FDD-OBS-06 | docs/FDD.md | Observabilidade | Log de rotação de secret | TRANSCRICAO | [09:21] Sofia |
| FDD-OBS-07 | docs/FDD.md | Observabilidade | Redact de secret e previousSecret no logger | CODIGO | src/shared/logger/index.ts |
| FDD-OBS-08 | docs/FDD.md | Métrica | Latência de entrega contra a meta de 10 s | TRANSCRICAO | [09:02] Marcos |
| FDD-OBS-09 | docs/FDD.md | Métrica | Duração da chamada HTTP perto do timeout | TRANSCRICAO | [09:42] Diego |
| FDD-OBS-10 | docs/FDD.md | Métrica | Backlog da outbox | TRANSCRICAO | [09:07] Bruno |
| FDD-OBS-11 | docs/FDD.md | Métrica | Volumetria e falhas por endpoint, insumo do rate limit | TRANSCRICAO | [09:39] Diego |
| FDD-OBS-12 | docs/FDD.md | Métrica | Itens na DLQ sem replay | TRANSCRICAO | [09:18] Diego |
| FDD-OBS-13 | docs/FDD.md | Tracing | Correlação por event_id e orderId | TRANSCRICAO | [09:25] Diego |
| FDD-INT-01 | docs/FDD.md | Integração | changeStatus chama publishWebhookEvent na transação | CODIGO | src/modules/orders/order.service.ts |
| FDD-INT-02 | docs/FDD.md | Integração | Toda transição de canTransition gera evento | CODIGO | src/modules/orders/order.status.ts |
| FDD-INT-03 | docs/FDD.md | Integração | Erros WEBHOOK_* estendendo AppError, BadRequestError e ConflictError | CODIGO | src/shared/errors/app-error.ts |
| FDD-INT-04 | docs/FDD.md | Integração | errorMiddleware sem alteração | CODIGO | src/middlewares/error.middleware.ts |
| FDD-INT-05 | docs/FDD.md | Integração | authenticate e requireRole('ADMIN') | CODIGO | src/middlewares/auth.middleware.ts |
| FDD-INT-06 | docs/FDD.md | Integração | Schemas Zod com validate | CODIGO | src/middlewares/validate.middleware.ts |
| FDD-INT-07 | docs/FDD.md | Integração | Novos routers /webhooks e /admin/webhooks | CODIGO | src/routes/index.ts |
| FDD-INT-08 | docs/FDD.md | Integração | buildControllers instancia o módulo | CODIGO | src/app.ts |
| FDD-INT-09 | docs/FDD.md | Integração | src/worker.ts no modelo de bootstrap e shutdown | CODIGO | src/server.ts |
| FDD-INT-10 | docs/FDD.md | Integração | createPrismaClient() no worker | CODIGO | src/config/database.ts |
| FDD-INT-11 | docs/FDD.md | Integração | Novas variáveis de ambiente no envSchema | CODIGO | src/config/env.ts |
| FDD-INT-12 | docs/FDD.md | Integração | redactPaths e logger.child | CODIGO | src/shared/logger/index.ts |
| FDD-INT-13 | docs/FDD.md | Integração | paginated() na listagem | CODIGO | src/shared/http/response.ts |
| FDD-INT-14 | docs/FDD.md | Integração | Models novos e migration | CODIGO | prisma/schema.prisma |
| FDD-INT-15 | docs/FDD.md | Integração | Scripts worker e start:worker; build já cobre src/**/*.ts | CODIGO | package.json |
| FDD-INT-16 | docs/FDD.md | Integração | Limpeza das tabelas novas e factories de teste | CODIGO | tests/setup.ts |
| FDD-DEP-01 | docs/FDD.md | Dependência | Node 20 com fetch e crypto nativos; uuid já instalado | CODIGO | package.json |
| FDD-DEP-02 | docs/FDD.md | Compatibilidade | Migration apenas aditiva | CODIGO | prisma/schema.prisma |
| FDD-DEP-03 | docs/FDD.md | Compatibilidade | Contrato de PATCH /orders/:id/status mantido | CODIGO | src/modules/orders/order.routes.ts |
| FDD-DEP-04 | docs/FDD.md | Dependência | Dois processos no deploy | TRANSCRICAO | [09:11] Diego |
| FDD-DEP-05 | docs/FDD.md | Compatibilidade | Rollback sem perder eventos registrados | TRANSCRICAO | [09:06] Diego |
| FDD-DEP-06 | docs/FDD.md | Dependência | Revisão da Sofia e portal do Marcos | TRANSCRICAO | [09:46] Sofia |
| FDD-AC-01 | docs/FDD.md | Critério de aceite | Uma linha por endpoint elegível no mesmo commit | TRANSCRICAO | [09:34] Bruno |
| FDD-AC-02 | docs/FDD.md | Critério de aceite | Nenhuma linha sem endpoint elegível | TRANSCRICAO | [09:34] Bruno |
| FDD-AC-03 | docs/FDD.md | Critério de aceite | Falha na outbox desfaz a mudança de status | TRANSCRICAO | [09:40] Bruno |
| FDD-AC-04 | docs/FDD.md | Critério de aceite | Entrega abaixo de 10 s | TRANSCRICAO | [09:10] Larissa |
| FDD-AC-05 | docs/FDD.md | Critério de aceite | Cinco headers e assinatura válida | TRANSCRICAO | [09:45] Diego |
| FDD-AC-06 | docs/FDD.md | Critério de aceite | Intervalos de retry e DLQ com attempts = 6 | TRANSCRICAO | [09:17] Larissa |
| FDD-AC-07 | docs/FDD.md | Critério de aceite | Timeout em 10 s | TRANSCRICAO | [09:42] Diego |
| FDD-AC-08 | docs/FDD.md | Critério de aceite | Payload acima de 64 KB vai para a DLQ | TRANSCRICAO | [09:24] Larissa |
| FDD-AC-09 | docs/FDD.md | Critério de aceite | URL http rejeitada | TRANSCRICAO | [09:23] Sofia |
| FDD-AC-10 | docs/FDD.md | Critério de aceite | Dupla assinatura por 24 h | TRANSCRICAO | [09:21] Sofia |
| FDD-AC-11 | docs/FDD.md | Critério de aceite | Replay restrito a ADMIN, com log | TRANSCRICAO | [09:36] Sofia |
| FDD-AC-12 | docs/FDD.md | Critério de aceite | Histórico com no máximo 100 itens | TRANSCRICAO | [09:34] Marcos |
| FDD-AC-13 | docs/FDD.md | Critério de aceite | Secret fora de listagens e logs | TRANSCRICAO | [09:22] Diego |
| FDD-AC-14 | docs/FDD.md | Critério de aceite | Reenvio após queda do worker | TRANSCRICAO | [09:24] Diego |
| FDD-AC-15 | docs/FDD.md | Critério de aceite | Ordem por pedido mesmo com retry | TRANSCRICAO | [09:12] Diego |
| FDD-AC-16 | docs/FDD.md | Critério de aceite | Suíte existente continua passando | CODIGO | tests/orders.test.ts |
| FDD-RISCO-01 | docs/FDD.md | Risco | Duplicata após queda do worker | TRANSCRICAO | [09:24] Diego |
| FDD-RISCO-02 | docs/FDD.md | Risco | Crescimento da outbox sem arquivamento | TRANSCRICAO | [09:08] Diego |
| FDD-RISCO-03 | docs/FDD.md | Risco | Evento em retry segura os seguintes do mesmo pedido | TRANSCRICAO | [09:13] Larissa |
| FDD-RISCO-04 | docs/FDD.md | Risco | Worker parado sem alerta | TRANSCRICAO | [09:11] Diego |
| FDD-RISCO-05 | docs/FDD.md | Risco | Rajada de eventos para um cliente | TRANSCRICAO | [09:38] Diego |
| FDD-RISCO-06 | docs/FDD.md | Risco | URL apontando para rede interna; não discutido, pauta da revisão de segurança | TRANSCRICAO | [09:46] Sofia |
| FDD-RISCO-07 | docs/FDD.md | Risco | Rotação dupla em menos de 24 h | TRANSCRICAO | [09:21] Sofia |
