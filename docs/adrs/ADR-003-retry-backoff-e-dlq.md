# ADR-003: Retry com backoff exponencial e DLQ em tabela separada

## Status

Aceito

- **Data da decisão:** reunião técnica de quinta-feira, 09:00 ([TRANSCRICAO.md](../../TRANSCRICAO.md))
- **Decisores:** Larissa (Tech Lead), Diego (Plataforma), Bruno (Pedidos), Marcos (Produto), Sofia (Segurança)
- **Relacionados:** [ADR-002](ADR-002-worker-em-processo-separado.md), [ADR-005](ADR-005-at-least-once-com-x-event-id.md)

## Contexto

O endpoint do cliente pode estar fora do ar, lento ou respondendo erro. Já houve cliente com indisponibilidade de duas horas em manutenção planejada ([09:16] Diego). A política de retry precisa cobrir janelas desse tamanho sem deixar eventos pendurados para sempre.

## Decisão

- **Backoff exponencial com teto de 5 retentativas**, nos intervalos **1 min, 5 min, 30 min, 2 h e 12 h** ([09:17] Diego, [09:17] Larissa).
- **Contagem:** um envio inicial e, a cada falha, uma retentativa após o intervalo seguinte. A 5ª retentativa acontece cerca de 14 h 36 min depois da primeira falha (1 + 5 + 30 + 120 + 720 minutos), que é o "quase 15 horas entre primeira falha e última tentativa" calculado por Diego ([09:17] Diego). No resumo final, Larissa fala em "total 5 tentativas" ([09:48] Larissa); este ADR lê "tentativa" como retentativa, porque é a única leitura em que os cinco intervalos e a janela de quase 15 horas fecham ao mesmo tempo. Marcos aceitou essa janela como suficiente ([09:17] Marcos).
- **Após a falha da 5ª retentativa**, o evento é considerado falha permanente e vai para a **DLQ** ([09:15] Diego).
- **A DLQ é uma tabela separada, `webhook_dead_letter`**, com payload, motivo da falha e timestamp ([09:18] Diego).
- **Reprocessamento manual** por `POST /admin/webhooks/dead-letter/:id/replay`, que recoloca o evento na outbox como pendente ([09:18] Diego). O endpoint exige role `ADMIN` e registra quem fez o replay ([09:36] Sofia, [09:36] Larissa).
- **Timeout de 10 segundos** por chamada HTTP; sem resposta nesse prazo, conta como falha e entra no retry ([09:42] Diego).

## Alternativas Consideradas

1. **3 retentativas.** Descartado. Uma indisponibilidade de manhã esgotaria as tentativas em cerca de 30 minutos ([09:16] Bruno, [09:16] Diego).
2. **Retry indefinido com backoff.** Descartado. O evento ficaria pendurado para sempre se o cliente sumisse ([09:15] Diego).
3. **Marcar como `failed` na própria outbox, sem tabela de DLQ.** Descartado. A tabela separada deixa a leitura da outbox mais limpa e guarda a evidência para debug e reprocessamento ([09:17] Larissa, [09:18] Diego).
4. **Notificar o cliente por e-mail após falhas seguidas.** Fora desta fase ([09:37] Marcos, [09:37] Larissa).

## Consequências

**Positivas**

- Cobre indisponibilidades de até cerca de 15 horas sem intervenção humana.
- Tempo de vida do evento limitado: nenhum evento fica na outbox indefinidamente.
- A DLQ guarda evidência para investigar e reprocessar sem poluir a outbox.

**Negativas**

- Um evento pode chegar ao cliente com quase 15 horas de atraso.
- Eventos em DLQ só voltam por ação de um administrador; não há aviso automático ao cliente nesta fase.
- Cada retentativa é um novo envio do mesmo evento, o que reforça a necessidade de deduplicação no cliente ([ADR-005](ADR-005-at-least-once-com-x-event-id.md)).

**Trade-off explícito:** entre perder pouco (teto curto) e nunca desistir (retry infinito), escolheu-se uma janela finita de quase 15 horas e uma DLQ auditável. Marcos considerou que um cliente fora do ar por 15 horas "já tá com problema sério dele" ([09:17] Marcos).
