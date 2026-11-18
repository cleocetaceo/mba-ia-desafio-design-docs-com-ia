# Design Docs com IA: Sistema de Webhooks de Notificação de Pedidos

Entrega do desafio **"Da Reunião ao Documento: Design Docs Gerados por IA"** (MBA Engenharia de Software com IA, Full Cycle). O enunciado original está no [repositório base](https://github.com/devfullcycle/mba-ia-desafio-design-docs-com-ia/blob/main/README.md).

## Sobre o desafio

Uma empresa que opera um Order Management System em produção decidiu, numa reunião de cerca de 55 minutos, construir um sistema de webhooks para avisar clientes B2B quando o status dos pedidos muda. Nada foi registrado além da transcrição. A tarefa foi transformar essa transcrição e o código existente (Node.js, TypeScript, Express, Prisma e MySQL) num pacote de design docs acionável: PRD, RFC, FDD, ADRs e um tracker de rastreabilidade.

A regra central é que nada pode ser inventado. Cada requisito, decisão e restrição precisa apontar para uma fala da reunião ou para um arquivo real do repositório. Isso exige separar o que foi decidido do que foi descartado ou adiado. O código da aplicação (`src/`, `prisma/`, `tests/`) não foi alterado: a entrega é só documental.

## Workflow adotado

1. **Contexto antes de texto.** O agente leu o enunciado, a transcrição completa e os arquivos de integração do OMS: `order.service.ts`, `order.status.ts`, `app-error.ts`, `http-errors.ts`, `error.middleware.ts`, `auth.middleware.ts`, `schema.prisma`, `server.ts`, `app.ts`, `routes/index.ts` e o logger.
2. **Plano em modo Plan.** Antes de escrever, o agente produziu um plano com o que a reunião fechou, o que ficou fora, a fronteira entre os documentos e a lista de ADRs. O plano foi revisado e ajustado antes da execução (ver "Iterações e ajustes").
3. **Ordem de produção**, a sugerida no enunciado:
   1. **ADRs**, como esqueleto das decisões (7 arquivos, cobrindo as 6 decisões obrigatórias mais o snapshot de payload);
   2. **RFC**, consolidando a proposta e linkando os ADRs, sem detalhe de implementação;
   3. **FDD**, o nível de implementação, com a seção "Integração com o sistema existente" construída arquivo por arquivo;
   4. **PRD**, por último entre os grandes documentos, como consolidação em linguagem de produto;
   5. **TRACKER**, varrendo os documentos prontos;
   6. **README**.
4. **IDs dentro dos documentos.** Cada item verificável do PRD, RFC e FDD recebeu um ID entre colchetes (`[PRD-FR-01]`, `[FDD-CONTRATO-05]`...), e o tracker usa os mesmos IDs. Assim, qualquer linha do tracker localiza o item no documento e vice-versa.
5. **Verificação automática e revisão.** O script de verificação rodou sobre o tracker e sobre todas as citações `[hh:mm] Nome` dos documentos. Depois, o pacote foi revisado contra a checklist de critérios de aceite do enunciado.

Interação com a IA: o kickoff foi um pedido curto, e o refinamento aconteceu no plano. As duas mensagens que abriram o trabalho foram:

```text
Analise o documento @docs/mba-ia-desafio-design-docs-com-ia.md e planeje a implementação
```

```text
Para autenticar no GitHub analise o projeto @mba-ia-desafio-ingestao-busca
```

## Prompts customizados

Os prompts abaixo foram escritos para este desafio. Eles direcionaram o agente na classificação da transcrição, na seção de integração do FDD e na auditoria do tracker.

**1. Classificação da transcrição (decidido, descartado, adiado)**

```text
Leia TRANSCRICAO.md inteira. Classifique cada fala relevante em exatamente uma
categoria: DECISÃO FECHADA, REQUISITO, RESTRIÇÃO, DESCARTADO, ADIADO/EM ABERTO.

Para cada item devolva: categoria | resumo de uma linha | [hh:mm] Nome.

Regras:
- Só é DECISÃO o que alguém fechou explicitamente ("decidido", "anotado",
  "tá decidido", ou o resumo final da Larissa às 09:48).
- Ideias sugeridas e recusadas vão para DESCARTADO, com o motivo dito na reunião.
- "Observar e decidir depois", "próxima fase" e "problema do futuro" vão para ADIADO.
- Não deduza requisito que ninguém falou. Se faltar informação, diga que faltou.
- Se o resumo final contradiz uma fala anterior (números, contagens), aponte a
  contradição com os dois timestamps em vez de escolher um lado.
- Nomes técnicos que a reunião não definiu (paths, nomes de tabela) devem ser
  marcados como "derivado".
```

**2. Seção "Integração com o sistema existente" do FDD**

```text
Escreva a seção "Integração com o sistema existente" do docs/FDD.md.

Antes de citar qualquer arquivo, abra-o no repositório e confirme que existe.
Para cada arquivo, informe:
- o que muda no PR de implementação, usando os nomes reais de funções e classes
  (changeStatus, $transaction, AppError, BadRequestError, requireRole,
  buildApiRouter, createPrismaClient, redactPaths...); ou
- "sem alteração", com o motivo (ex.: errorMiddleware já trata AppError).

Restrições:
- No mínimo 4 caminhos reais; arquivos que ainda não existem (src/worker.ts,
  src/modules/webhooks/*) devem ser marcados como NOVOS.
- Nenhuma dependência npm nova. Se algo exigir uma, sinalize em vez de propor.
- Respeite o que a reunião fixou: publishWebhookEvent(tx, order, fromStatus,
  toStatus) recebe o tx da transação ([09:41] Bruno); PrismaClient separado no
  worker ([09:30] Bruno).
- Este pacote é documental: descreva a mudança, não edite src/, prisma/ ou tests/.
```

**3. Auditoria do tracker contra alucinação**

```text
Gere um script Node que leia docs/TRACKER.md e TRANSCRICAO.md e verifique:
1. toda Localização com Fonte=TRANSCRICAO corresponde a uma linha "[hh:mm] Nome:"
   que existe na transcrição (mesmo horário e mesmo falante);
2. toda Localização com Fonte=CODIGO é um caminho que existe no repositório;
3. todo ID entre colchetes em PRD, RFC e FDD tem uma linha no tracker;
4. toda citação [hh:mm] Nome dentro dos documentos existe na transcrição;
5. o percentual de linhas TRANSCRICAO (mínimo 70%) e o total de linhas CODIGO (mínimo 5).
Liste cada problema encontrado; não corrija nada automaticamente.
```

## Iterações e ajustes

O resultado final passou por **quatro iterações principais**: plano inicial, plano ajustado, primeira escrita completa e correções da revisão e da verificação.

1. **Contagem do retry.** O plano inicial lia o resumo final, "5 tentativas, backoff 1m/5m/30m/2h/12h" ([09:48] Larissa), como 5 envios no total, com a espera de 12 h antes de mandar o evento para a DLQ sem novo envio. Na escrita do ADR-003, a revisão contra a transcrição mostrou que essa leitura contradiz Diego: "total de quase 15 horas entre primeira falha e última tentativa" ([09:17] Diego). Com 5 envios, a última tentativa aconteceria cerca de 2 h 36 min depois da primeira falha. A leitura corrigida é **envio inicial mais 5 retentativas**: 1 + 5 + 30 + 120 + 720 = 876 minutos, ou 14 h 36 min, que é o "quase 15 horas". O ADR-003 registra a ambiguidade e o porquê da escolha, e a tabela de retry do FDD mostra os 6 envios.
2. **Autenticação no GitHub.** O primeiro plano deixava a publicação em aberto. A pedido, o agente analisou o projeto `mba-ia-desafio-ingestao-busca`: o remote é HTTPS sem token, e a credencial vem do `gh` autenticado no keyring. O plano passou a prever o fork para `cleocetaceo`, `upstream` apontando para o repositório base e push só depois da revisão.
3. **Itens que a IA tende a "completar" e a reunião não fechou.** A revisão removeu ou marcou como derivado:
   - o path de rotação de secret (`/rotate-secret`): Sofia pediu o endpoint, mas não o nomeou; o FDD e o tracker dizem isso;
   - `WEBHOOK_SECRET_REQUIRED`: Bruno citou como exemplo de código, mas a secret é gerada pela plataforma; o FDD explica por que o código não entra;
   - o fan-out por endpoint (uma linha de outbox por webhook): não foi votado com esse nome; ficou justificado como consequência de secret, filtro e histórico serem por endpoint;
   - o risco de URL apontando para rede interna: não foi discutido; entrou como pauta da revisão de segurança da Sofia, sem virar requisito.

   E-mail, dashboard, rate limiting, arquivamento e escala de workers ficaram explicitamente fora de escopo ou em aberto.
4. **Ajustes estruturais apontados pela verificação.**
   - O RFC não tinha IDs, então o tracker não conseguia apontar para os itens dele. O RFC ganhou `RFC-PROP`, `RFC-ALT`, `RFC-OPEN` e `RFC-RISCO`.
   - A reordenação das seções do FDD (resiliência antes de observabilidade) quebrou um link do RFC, que foi corrigido.
   - O limite de 64 KB saiu da transação de `changeStatus` e foi para o worker. Assim, um evento grande nunca bloqueia a mudança de status, e o "não envia, dá erro" da Sofia continua valendo.

   Depois disso, o script de verificação passou sem pendências: 297 linhas no tracker, 85,9% com fonte TRANSCRICAO, 42 com CODIGO, 216 IDs cobertos e nenhuma fala ou caminho inexistente.

## Como navegar a entrega

| Ordem | Arquivo | O que responde |
|---|---|---|
| 1 | [docs/PRD.md](docs/PRD.md) | Por que e o quê: problema, público, escopo, requisitos e métricas |
| 2 | [docs/RFC.md](docs/RFC.md) | A proposta técnica em 2 a 4 páginas, alternativas descartadas e questões em aberto |
| 3 | [docs/adrs/](docs/adrs/) | Uma decisão por arquivo, com contexto, alternativas e consequências |
| 4 | [docs/FDD.md](docs/FDD.md) | Como construir: modelo de dados, fluxos, contratos HTTP, erros `WEBHOOK_*`, resiliência, observabilidade e integração com o código |
| 5 | [docs/TRACKER.md](docs/TRACKER.md) | De onde veio cada item, por timestamp da reunião ou caminho de arquivo |

ADRs:

- [ADR-001: Padrão Outbox no MySQL existente](docs/adrs/ADR-001-outbox-no-mysql.md)
- [ADR-002: Worker em processo separado, com polling de 2 segundos](docs/adrs/ADR-002-worker-em-processo-separado.md)
- [ADR-003: Retry com backoff exponencial e DLQ em tabela separada](docs/adrs/ADR-003-retry-backoff-e-dlq.md)
- [ADR-004: Assinatura HMAC-SHA256 com secret por endpoint e rotação](docs/adrs/ADR-004-hmac-sha256-secret-por-endpoint.md)
- [ADR-005: Garantia at-least-once com deduplicação por X-Event-Id](docs/adrs/ADR-005-at-least-once-com-x-event-id.md)
- [ADR-006: Reuso dos padrões existentes do projeto](docs/adrs/ADR-006-reuso-dos-padroes-do-projeto.md)
- [ADR-007: Payload renderizado (snapshot) na inserção da outbox](docs/adrs/ADR-007-snapshot-de-payload-na-insercao.md)

Fonte primária: [TRANSCRICAO.md](TRANSCRICAO.md), mantida sem alteração.

## Critérios do desafio

O código da aplicação (`src/`, `prisma/`, `tests/`) e a `TRANSCRICAO.md` não foram alterados. A conferência abaixo usa a checklist do enunciado.

| Critério | Situação |
|---|---|
| PRD com as 12 seções, 16 requisitos funcionais, meta quantitativa (p95 abaixo de 10 s), 8 itens fora de escopo e 6 riscos com probabilidade, impacto e mitigação | Atendido |
| RFC com metadados e revisores da reunião, alternativas descartadas com trade-off, questões em aberto e links para os 7 ADRs | Atendido |
| FDD com fluxos, 7 endpoints com request, response e status, erros `WEBHOOK_*`, resiliência, logs, métricas, tracing e integração em mais de 4 arquivos reais | Atendido |
| 7 ADRs no formato `ADR-NNN-titulo.md`, com Status, Contexto, Decisão, Alternativas e Consequências, cobrindo as 6 decisões principais. O ADR-006 cita arquivos reais | Atendido |
| Tracker com as 6 colunas, 297 linhas, 85,9% com fonte `TRANSCRICAO` e 42 com `CODIGO`. Os 216 IDs dos documentos têm linha | Atendido |

Dois pontos não são itens faltando, mas são os únicos que um avaliador pode questionar.

- A Larissa falou "total 5 tentativas" e o FDD registra 6 envios (1 inicial mais 5 retentativas) para fechar a conta de quase 15 horas do Diego. O [ADR-003](docs/adrs/ADR-003-retry-backoff-e-dlq.md) explica essa escolha.
- O FDD nomeia arquivos que ainda não existem, como `src/worker.ts` e `src/modules/webhooks/`, e os marca como novos. A seção de integração também cita caminhos que já existem no repositório.
