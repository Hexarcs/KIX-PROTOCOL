# PRISMA-5 — Codificadores de Faturas Multi-Formato

## O que a spec define

Máxima interoperabilidade no PDV: geração e conversão dinâmica de formatos nativos de cobrança e esquemas proprietários de contingência.

## Modos

| Ícone | Modo | Descrição |
|---|---|---|
| ⚡ | Native Standard | BOLT 11 (uso único) e Offers/BOLT 12 |
| 🔗 | Unified Universal | QR BIP 21 agrupando on-chain e Lightning, com LNURL-Pay |
| 🪙 | Contingency & Tokenized | eCash (Cashu/Fedimint) ou vouchers locais com liquidação enfileirada |

## Matriz de formatos

| Formato | Natureza | Reutilizável? | Suporte | Uso típico |
|---|---|---|---|---|
| BOLT 11 | Invoice Lightning | Não (uso único) | Universal | Cobrança por pedido |
| BOLT 12 | Offer/invoice | Sim (offer) | Crescente; varia por engine/carteira | Link reutilizável, blinded paths |
| BIP 21 | URI `bitcoin:` | Sim | Amplo | On-chain + fallback Lightning |
| LNURL-Pay | Endpoint bech32 | Sim | Amplo | QR estático que busca invoice |
| Lightning Address | `user@dominio` | Sim | Amplo | Alias para LNURL-Pay |
| eCash (Cashu/Fedimint) | Token | Sim | Nicho | Contingência/offline; custodial |

## Ciclo de vida de uma cobrança

1. **Cotação**: preço em BRL → sats (fonte de câmbio explícita e timestamp).
2. **Criação**: valor, memo/referência, expiração.
3. **Exibição**: QR + deep link + código copiável.
4. **Detecção**: `SubscribeInvoices`/`waitinvoice` ou polling `LookupInvoice`.
5. **Conciliação**: `r_hash`/payment hash como chave idempotente; valor efetivamente recebido.
6. **Expiração**: regenerar se vencer; nunca reutilizar BOLT 11.
7. **Reembolso**: exige liquidez de saída e uma invoice de destino.

## Segurança e correção

- Preimage é segredo: não logue, não exponha, não use como dado público.
- Verifique **valor recebido ≥ valor esperado**; trate under/overpayment.
- Idempotência obrigatória em webhooks e callbacks.
- Expiração curta para preço volátil; informe o cliente.
- LNURL e Lightning Address dependem de HTTPS e resolução confiável; cuidado com phishing de QR.
- eCash é custodial (mint/federação); não venda como soberano.

## Prática recomendada (fora da spec)

- BOLT 11 por pedido como padrão; LNURL-Pay ou BOLT 12 quando o caso pede QR reutilizável.
- BIP 21 como fallback on-chain para carteiras sem Lightning.
- Teste com várias carteiras reais antes de produção.
- No Brasil, Pix fica **fora** do PRISMA-5; se existir, trate como trilho adjacente com conciliação própria.

## Anti-padrões

- QR BOLT 11 estático reutilizado para vários pedidos.
- Fatura sem expiração.
- Confiar no valor enviado pelo cliente.
- Misturar Pix e Lightning sem reconciliar os dois.
- Usar eCash como padrão em vez de contingência.

## Perguntas de diagnóstico

- O QR precisa ser reutilizável? (se sim: LNURL/BOLT 12)
- Qual a compatibilidade de carteiras do público?
- Como o preço BRL→sats é cotado e por quanto tempo vale?
- O que acontece se a rede cair no meio do caixa?
