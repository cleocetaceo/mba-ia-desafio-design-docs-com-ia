# ADR-002: Worker em processo separado, com polling de 2 segundos

## Status

Aceito

- **Data da decisão:** reunião técnica de quinta-feira, 09:00 ([TRANSCRICAO.md](../../TRANSCRICAO.md))
- **Decisores:** Larissa (Tech Lead), Diego (Plataforma), Bruno (Pedidos), Marcos (Produto)
- **Relacionados:** [ADR-001](ADR-001-outbox-no-mysql.md), [ADR-003](ADR-003-retry-backoff-e-dlq.md), [ADR-006](ADR-006-reuso-dos-padroes-do-projeto.md)

## Contexto

Com a outbox ([ADR-001](ADR-001-outbox-no-mysql.md)), alguém precisa ler os eventos pendentes e disparar as chamadas HTTP. Duas perguntas ficaram abertas no início da reunião: como esse leitor descobre que há eventos novos, e onde ele roda.

O MySQL não tem listener nativo equivalente ao `LISTEN/NOTIFY` do Postgres. Triggers do MySQL só executam SQL e não notificam processos externos ([09:09] Diego).

A API hoje tem uma única entry-point, `src/server.ts`, que sobe o Express e instancia o `PrismaClient` exportado por `src/config/database.ts`.

## Decisão

- **Polling em loop a cada 2 segundos.** O worker busca os eventos elegíveis mais antigos em lote pequeno, processa e atualiza o status ([09:09] Diego). A latência mínima de 2 segundos no pior caso foi aceita e atende o requisito de menos de 10 segundos ([09:10] Marcos, [09:10] Larissa).
- **Processo separado da API.** Se a API reinicia, o worker não pode cair junto ([09:11] Diego).
- **Nova entry-point `src/worker.ts`** e script `npm run worker`, no mesmo modelo de `src/server.ts` ([09:11] Larissa). A lógica de processamento fica dentro do módulo, em `src/modules/webhooks/webhook.worker.ts` ([09:28] Bruno).
- **Mesmo banco e mesma stack**, com um `PrismaClient` próprio do processo do worker: mesma `DATABASE_URL`, instância nova, porque `PrismaClient` é por processo ([09:11] Bruno, [09:11] Diego, [09:30] Bruno). A fábrica `createPrismaClient()` de `src/config/database.ts` já serve para isso.
- **Um único worker.** A ordem de entrega segue `created_at` da outbox, e a garantia de ordem vale **por `order_id` e enquanto houver um só worker**. Não há garantia de ordem global ([09:12] Diego, [09:13] Larissa). Os clientes não pediram ordem global ([09:14] Marcos).

## Alternativas Consideradas

1. **Trigger no MySQL para tornar o worker reativo.** Descartado. A trigger não consegue avisar um processo externo; seria preciso improvisar escrita em arquivo ou chamada a um endpoint ([09:09] Bruno, [09:09] Diego).
2. **Worker dentro do mesmo processo da API.** Descartado. Um restart da API derrubaria o worker ([09:11] Diego).
3. **Vários workers em paralelo.** Adiado. Perde a garantia de ordem; se for preciso escalar, as opções citadas são particionar por `order_id` ou usar lock pessimista, como "problema do futuro" ([09:12] Diego, [09:13] Diego).

## Consequências

**Positivas**

- Implementação simples, sem dependência de recurso que o MySQL não tem.
- API e worker reiniciam de forma independente.
- Reaproveita a configuração existente (`src/config/env.ts`, `src/config/database.ts`, `src/shared/logger/index.ts`).

**Negativas**

- Consultas periódicas ao banco mesmo sem eventos.
- Até 2 segundos de atraso antes de o evento ser lido.
- Um ponto único de processamento: se o worker parar, as entregas param (os eventos continuam seguros na outbox).
- A ordem por `order_id` é uma limitação conhecida, e deixa de valer se o número de workers aumentar.

**Trade-off explícito:** aceita-se latência de até 2 segundos e um único worker em troca de simplicidade e ordem por pedido. Escalar horizontalmente exige uma nova decisão sobre particionamento ou lock.
