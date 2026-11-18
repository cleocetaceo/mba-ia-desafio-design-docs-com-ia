# ADR-004: Assinatura HMAC-SHA256 com secret por endpoint e rotação

## Status

Aceito

- **Data da decisão:** reunião técnica de quinta-feira, 09:00 ([TRANSCRICAO.md](../../TRANSCRICAO.md))
- **Decisores:** Sofia (Segurança), Larissa (Tech Lead), Diego (Plataforma), Bruno (Pedidos)
- **Relacionados:** [ADR-005](ADR-005-at-least-once-com-x-event-id.md), [ADR-006](ADR-006-reuso-dos-padroes-do-projeto.md)

## Contexto

Os webhooks são **outbound**: saem da plataforma para os clientes, nunca o contrário ([09:02] Sofia, [09:02] Marcos). Eles levam dados de pedidos para um endpoint fora da infraestrutura da empresa. O cliente precisa validar que a requisição veio da plataforma e que o payload não foi adulterado no caminho ([09:19] Sofia).

Já houve cliente que vazou uma secret em log da própria aplicação ([09:22] Diego).

## Decisão

- **HMAC-SHA256 sobre o corpo do request**, enviado no header `X-Signature`. O cliente recalcula e compara ([09:20] Sofia).
- **Secret única por endpoint de webhook**, gerada pela plataforma e devolvida ao cliente na criação. Não existe secret global ([09:21] Sofia, [09:31] Marcos).
- **A configuração do webhook armazena URL, secret, `customer_id` e estado ativo** ([09:21] Bruno, [09:21] Sofia).
- **Rotação pela API:** o cliente pede uma nova secret; a anterior continua válida por **24 horas** em paralelo e depois deixa de valer ([09:21] Sofia, [09:22] Sofia).
- **URL obrigatoriamente `https`**, validada no schema Zod do módulo; URL `http` é recusada com erro de validação. Sofia tratou isso como validação, sem peso de decisão arquitetural ([09:23] Sofia).
- **Revisão de segurança** de pelo menos dois dias úteis antes do deploy, com foco no HMAC e na geração de secret ([09:46] Sofia).

O formato exato do header durante a janela de rotação está no [FDD](../FDD.md#62-assinatura-hmac).

## Alternativas Consideradas

1. **Secret global da plataforma.** Descartado. Se uma secret vaza, todos os clientes ficam expostos ([09:21] Sofia).
2. **Rotação com corte imediato da secret antiga.** Descartado. O cliente precisa de tempo para migrar os próprios sistemas, daí as 24 horas em paralelo ([09:21] Sofia).

## Consequências

**Positivas**

- O cliente autentica a origem e a integridade de cada entrega com uma biblioteca padrão de mercado ([09:20] Sofia).
- O vazamento de uma secret afeta apenas um endpoint, e o cliente se recupera sozinho com a rotação.

**Negativas**

- A plataforma precisa guardar a secret de forma recuperável, porque o HMAC exige a secret original para assinar. Ela não pode ser guardada apenas como hash, como acontece com senhas.
- Durante as 24 horas de rotação, o worker trabalha com duas secrets por endpoint.
- A validação da assinatura depende do cliente implementá-la; a plataforma não tem como verificar isso.

**Trade-off explícito:** ganha-se isolamento entre clientes e autonomia de rotação, ao custo de gerenciar uma secret por endpoint e uma janela de convivência entre duas secrets.
