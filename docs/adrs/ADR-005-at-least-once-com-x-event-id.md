# ADR-005: Garantia at-least-once com deduplicação por `X-Event-Id`

## Status

Aceito

- **Data da decisão:** reunião técnica de quinta-feira, 09:00 ([TRANSCRICAO.md](../../TRANSCRICAO.md))
- **Decisores:** Diego (Plataforma), Larissa (Tech Lead), Sofia (Segurança), Marcos (Produto)
- **Relacionados:** [ADR-001](ADR-001-outbox-no-mysql.md), [ADR-003](ADR-003-retry-backoff-e-dlq.md)

## Contexto

Outbox, worker e retry ([ADR-001](ADR-001-outbox-no-mysql.md), [ADR-002](ADR-002-worker-em-processo-separado.md), [ADR-003](ADR-003-retry-backoff-e-dlq.md)) criam situações em que o mesmo evento é enviado mais de uma vez. Por exemplo: o cliente processa, mas a resposta não chega em 10 segundos; ou o worker cai entre o envio e a gravação do resultado. A plataforma precisa definir qual garantia de entrega oferece e como o cliente lida com duplicatas.

## Decisão

- **Garantia at-least-once.** O cliente pode receber o mesmo evento mais de uma vez e precisa estar preparado ([09:24] Diego).
- **Cada evento leva um UUID no header `X-Event-Id`**, gerado quando o evento entra na outbox e único por evento. O cliente deduplica por esse valor ([09:25] Diego).
- O mesmo `X-Event-Id` se repete em todas as retentativas do evento e no replay a partir da DLQ, para que a deduplicação do cliente continue funcionando.
- **Documentação destacada** no portal do desenvolvedor, sob responsabilidade de Marcos ([09:26] Marcos).

Decisão registrada por Larissa às [09:26]: "At-least-once com X-Event-Id pra dedup do lado do cliente."

## Alternativas Consideradas

1. **Exactly-once.** Descartado. Exigiria coordenação dos dois lados e fica muito mais complexo ([09:25] Diego).

## Consequências

**Positivas**

- Nenhum evento commitado se perde por falha temporária: na dúvida, a plataforma reenvia.
- É o modelo já adotado por Stripe e GitHub, familiar para os clientes ([09:25] Diego).
- O worker não precisa de protocolo de confirmação com o cliente.

**Negativas**

- A responsabilidade de deduplicar passa para o cliente ([09:25] Sofia).
- Cliente que não deduplica pode processar duas vezes a mesma mudança de status.

**Trade-off explícito:** a plataforma aceita entregar duplicado para nunca perder evento, e transfere ao cliente o custo da idempotência. Diego estimou que at-least-once com `event_id` resolve "99% dos casos" ([09:25] Diego).
