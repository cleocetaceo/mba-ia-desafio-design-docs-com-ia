# ADR-006: Reuso dos padrões existentes do projeto

## Status

Aceito

- **Data da decisão:** reunião técnica de quinta-feira, 09:00 ([TRANSCRICAO.md](../../TRANSCRICAO.md))
- **Decisores:** Larissa (Tech Lead), Bruno (Pedidos), Diego (Plataforma), Sofia (Segurança)
- **Relacionados:** [ADR-002](ADR-002-worker-em-processo-separado.md), [ADR-004](ADR-004-hmac-sha256-secret-por-endpoint.md)

## Contexto

O OMS tem padrões consistentes em todos os domínios. A feature de webhooks pode reaproveitá-los ou trazer estrutura própria. Os padrões relevantes no código são:

- **Módulos por domínio** em `src/modules/<dominio>/` com controller, service, repository, routes e schemas, por exemplo `src/modules/orders/order.controller.ts`, `order.service.ts`, `order.repository.ts`, `order.routes.ts` e `order.schemas.ts`.
- **Erros de domínio** herdando de `AppError` (`src/shared/errors/app-error.ts`), com `statusCode` e um `errorCode` em caixa alta. Exemplos em `src/shared/errors/http-errors.ts`: `InvalidStatusTransitionError` (`INVALID_STATUS_TRANSITION`) e `InsufficientStockError` (`INSUFFICIENT_STOCK`).
- **Middleware de erro centralizado** em `src/middlewares/error.middleware.ts`, que já trata `AppError`, `ZodError` e erros conhecidos do Prisma (`P2002`, `P2025`).
- **Logger Pino** em `src/shared/logger/index.ts`, com `redact` de campos sensíveis.
- **Autenticação JWT** com `authenticate` e `requireRole` em `src/middlewares/auth.middleware.ts`; os roles são `ADMIN` e `OPERATOR`.
- **Validação Zod** pelo middleware `validate` em `src/middlewares/validate.middleware.ts`.
- **Composição** em `src/app.ts` (`buildControllers`) e registro de rotas em `src/routes/index.ts` (`buildApiRouter`).
- **Ids UUID** em todos os models de `prisma/schema.prisma`.

## Decisão

Reuso máximo do que já existe ([09:30] Larissa):

- **Módulo `src/modules/webhooks`**, com a mesma estrutura dos demais ([09:27] Bruno), mais `webhook.worker.ts` para o processamento ([09:28] Bruno).
- **Erros do módulo como subclasses de `AppError`**, com códigos de prefixo `WEBHOOK_`, como `WEBHOOK_NOT_FOUND` e `WEBHOOK_INVALID_URL` ([09:28] Bruno, [09:29] Larissa).
- **Pino como logger**, sem biblioteca nova de log ([09:29] Bruno).
- **`errorMiddleware` sem alteração**: os novos erros são `AppError` e passam pelo tratamento existente ([09:29] Bruno).
- **Schemas Zod** para o corpo e os parâmetros dos endpoints, incluindo a recusa de URL `http` ([09:23] Sofia, [09:30] Larissa).
- **`requireRole('ADMIN')`** já existente no endpoint de replay da DLQ ([09:36] Larissa).
- **`PrismaClient` separado no worker**, criado com a mesma `DATABASE_URL` ([09:30] Bruno).
- **Ids UUID** também nas tabelas novas ([09:51] Larissa).
- **Integração com pedidos por uma função**, sem injetar o repository inteiro: `publishWebhookEvent(tx, order, fromStatus, toStatus)` recebe o `Prisma.TransactionClient` da transação de `changeStatus` ([09:41] Bruno, [09:41] Diego).

## Alternativas Consideradas

1. **Estrutura própria para o módulo, com fila, logger ou tratamento de erro dedicados.** Descartado. Não há ganho que justifique um padrão novo, e o middleware existente já cobre os casos ([09:29] Bruno).
2. **Injetar o repository de webhooks no `OrderService`.** Descartado em favor da função que recebe o `tx`, que acopla menos ([09:41] Bruno, [09:41] Diego).
3. **Compartilhar a instância de `PrismaClient` entre API e worker.** Não é possível, porque são processos diferentes ([09:30] Bruno).

## Consequências

**Positivas**

- Quem conhece um módulo do projeto entende o de webhooks.
- O formato de erro da API (`{ error: { code, message, details } }`) continua o mesmo para os clientes.
- Nenhuma dependência nova de log, erro ou validação.

**Negativas**

- O módulo herda os limites dos padrões atuais. Exemplo: o CRUD aceita qualquer usuário autenticado, porque o projeto não tem vínculo entre usuário e cliente; o endurecimento foi adiado ([09:37] Sofia).
- `OrderService` passa a depender de uma função do módulo de webhooks.

**Trade-off explícito:** o módulo ganha consistência e velocidade de entrega ao seguir os padrões existentes, e herda as limitações deles.
