# ADR-007: Payload renderizado (snapshot) na inserção da outbox

## Status

Aceito

- **Data da decisão:** reunião técnica de quinta-feira, após a saída de Marcos e Sofia ([TRANSCRICAO.md](../../TRANSCRICAO.md))
- **Decisores:** Larissa (Tech Lead), Diego (Plataforma), Bruno (Pedidos)
- **Relacionados:** [ADR-001](ADR-001-outbox-no-mysql.md), [ADR-003](ADR-003-retry-backoff-e-dlq.md)

## Contexto

Uma linha da outbox pode ser enviada segundos ou até horas depois de criada, por causa do polling e das retentativas ([ADR-002](ADR-002-worker-em-processo-separado.md), [ADR-003](ADR-003-retry-backoff-e-dlq.md)). Nesse intervalo, o pedido pode mudar de novo ou ser excluído (a exclusão é permitida em `PENDING` e `CANCELLED`, conforme `OrderService.delete` em `src/modules/orders/order.service.ts`).

A questão levantada foi: a outbox guarda o payload já pronto ou só o `order_id`, montando o payload na hora do envio ([09:51] Bruno)?

## Decisão

**A outbox guarda o payload renderizado no momento da inserção**, dentro da transação de `changeStatus`. O evento reflete o estado do pedido quando o status mudou, e não o estado no momento do envio ([09:52] Larissa, [09:52] Diego). Decisão fechada por Bruno às [09:52].

O conteúdo do payload segue o formato definido na reunião ([09:43] Diego), detalhado no [FDD](../FDD.md#61-payload-do-evento).

## Alternativas Consideradas

1. **Guardar apenas `order_id` e renderizar no envio.** Descartado. Se o pedido muda depois, o evento passaria a descrever um estado diferente do que o disparou ([09:52] Larissa).

## Consequências

**Positivas**

- O evento é imutável: retentativas e replay da DLQ enviam exatamente o mesmo conteúdo, com a mesma assinatura sobre o mesmo corpo.
- O worker não consulta a tabela `orders` para enviar, e o envio funciona mesmo se o pedido for excluído.
- A outbox não precisa de chave estrangeira para `orders`.

**Negativas**

- Cada linha da outbox guarda uma cópia dos dados do pedido, o que aumenta o espaço ocupado.
- Para dados mais atuais, o cliente consulta `GET /orders/:id`, como previsto para os itens, que não entram no payload ([09:43] Diego).

**Trade-off explícito:** o evento custa mais espaço em troca de ser fiel ao momento da mudança e de um worker mais simples.
