# ADR-001: Padrão Outbox no MySQL existente

## Status

Aceito

- **Data da decisão:** reunião técnica de quinta-feira, 09:00 ([TRANSCRICAO.md](../../TRANSCRICAO.md))
- **Decisores:** Larissa (Tech Lead), Diego (Plataforma), Bruno (Pedidos)
- **Relacionados:** [ADR-002](ADR-002-worker-em-processo-separado.md), [ADR-005](ADR-005-at-least-once-com-x-event-id.md), [ADR-007](ADR-007-snapshot-de-payload-na-insercao.md)

## Contexto

Três clientes B2B (Atlas Comercial, MaxDistribuição e Nova Cargo) querem ser notificados quando o status dos pedidos muda, em menos de 10 segundos ([09:00] Marcos, [09:02] Marcos). Hoje eles fazem polling em `GET /orders`.

A mudança de status acontece em `OrderService.changeStatus` (`src/modules/orders/order.service.ts`), dentro de um `prisma.$transaction` que atualiza `orders`, insere em `order_status_history` e debita ou repõe `stock_quantity` conforme `shouldDebitStock` / `shouldReplenishStock` (`src/modules/orders/order.status.ts`). Bruno descreveu essa transação como "já pesada" ([09:04] Bruno).

O problema é registrar o evento de notificação com a mesma garantia da mudança de status, sem colocar uma chamada HTTP para um sistema externo dentro dessa transação.

## Decisão

Adotar o padrão **Transactional Outbox** no MySQL que a aplicação já usa (`prisma/schema.prisma`, `provider = "mysql"`):

- Uma tabela `webhook_outbox` recebe uma linha por evento, **na mesma transação SQL** que atualiza `orders` e `order_status_history` ([09:06] Diego).
- Se a transação principal faz commit, o evento existe. Se faz rollback, o evento some junto. Se o insert na outbox falhar, a mudança de status inteira dá rollback ([09:40] Bruno, [09:41] Diego).
- A tabela tem índice em `status` (pendente, processando, falhou, entregue) e em `created_at` ([09:08] Diego).
- A chave primária é UUID, seguindo o padrão do resto do schema (`@id @default(uuid()) @db.Char(36)` em todos os models) ([09:51] Larissa).
- A leitura e o disparo HTTP ficam com um worker separado ([ADR-002](ADR-002-worker-em-processo-separado.md)).

Decisão registrada por Larissa às [09:08]: "Tá decidido então: outbox em MySQL."

## Alternativas Consideradas

1. **Disparo HTTP síncrono dentro de `changeStatus`.** Descartado. Um cliente lento travaria a mudança de status de outros pedidos, e um cliente fora do ar não tem tratamento possível: não dá para fazer rollback da mudança de status porque o cliente não respondeu ([09:04] Bruno, [09:06] Diego).
2. **Redis Streams ou outra fila dedicada.** Descartado. Exigiria subir infraestrutura nova (Redis Cluster), o que Diego classificou como overengineering para um time pequeno ([09:07] Larissa, [09:07] Diego).

## Consequências

**Positivas**

- Consistência entre a mudança de status e o evento: não existe caso de status mudar sem evento, nem evento de uma mudança revertida.
- Nenhuma infraestrutura nova: mesmo banco, mesmo Prisma, mesmo backup.
- A latência da mudança de status cresce apenas o custo de um `INSERT` local.

**Negativas**

- O MySQL passa a fazer papel de fila. Volume alto de eventos aumenta a tabela; o arquivamento das linhas entregues (por volta de 30 dias) ficou fora do escopo desta feature ([09:08] Diego).
- A entrega depende de polling ([ADR-002](ADR-002-worker-em-processo-separado.md)), o que impõe latência mínima.

**Trade-off explícito:** troca-se a reatividade e a escala de uma fila dedicada pela garantia transacional e pela ausência de infraestrutura nova. Para o volume de três clientes B2B e o requisito de menos de 10 segundos, o ganho operacional pesa mais.
