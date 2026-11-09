# PRISMA-4 — Abstração do Motor Lightning

## O que a spec define

Camada de abstração e comutação entre motores de liquidação, tornando as interfaces de PDV agnósticas ao backend. Permite alternar entre motores locais ou hospedados por terceiros.

## Modos

| Ícone | Modo | Descrição |
|---|---|---|
| 🏢 | Native Enterprise | LND ou Core Lightning para nós soberanos de alto volume |
| 🧩 | Modular Engine | LNbits ou Alby Hub locais, para multi-tenant e isolamento por loja |
| 🤖 | Autonomous Server | Phoenixd, com gestão automática de liquidez via splicing |
| 📱 | Delegated Remote | Carteiras remotas via Nostr Wallet Connect (NWC), sem nó local |
| 🧰 | Embedded SDK | Provedores/backends baseados em LDK, com chaves mantidas pelo cliente |

## Comparativo prático

| Engine | Perfil | Pontos fortes | Cuidados |
|---|---|---|---|
| LND | Nó completo | Ecossistema amplo, gRPC/REST, macaroons, SCB, watchtower | Single-tenant para saldo; exige gestão de canais |
| Core Lightning (CLN) | Nó completo | Leve, plugins, offers/BOLT 12, PostgreSQL | Operação diferente; backup por `hsm_secret`/emergency recover |
| Eclair | Nó completo (JVM) | Implementação madura, foco ACINQ | Menos comum em stacks de loja; JVM consome RAM |
| Phoenixd | Servidor de carteira | Splicing e liquidez automática, operação simples | Depende de LSP; menos flexível que um nó completo |
| LNbits | Camada de contas | Multi-tenant, extensões, API amigável | Contas compartilham o nó/liquidez; custodial em relação ao operador |
| Alby Hub | Hub self-custodial | NWC, integração com apps | Entenda quem guarda as chaves e como backup funciona |
| LDK | Biblioteca embarcada | Chaves no app/usuário, integração profunda | Você constrói o resto (canais, backup, monitoramento) |
| NWC | Protocolo | Conecta app a carteira remota | Depende do serviço de carteira; não é soberano por si |

## Custódia e multi-tenancy

- **LND/CLN têm um saldo por nó.** Não existe subconta nativa por lojista.
- Para várias lojas: ledger próprio no app, **LNbits** (contas sobre um nó) ou **um nó por lojista** (isolamento real, custo alto).
- "Compartilhar LND" não é não-custodial: o operador do nó é custodiante.
- Do ponto de vista do lojista sem nó, o modo é **Delegated Remote** — mas isso não significa self-custody se o provedor segura as chaves.

## Liquidez

- **Receber** exige capacidade de entrada; **pagar/reembolsar** exige saída.
- Ferramentas: abertura de canais, LSP, canais JIT (LSPS2), liquidity ads (CLN), rebalanceamento, splicing (Phoenixd; suporte varia em LND/CLN).
- Sem plano de liquidez, o nó "funciona" mas não recebe — falha silenciosa comum.

## Backups e segurança

- LND: Static Channel Backup (SCB), arquivo `channel.backup`; teste a restauração.
- CLN: `hsm_secret` + arquivo de emergency recover; teste.
- Eclair: backup do diretório de dados; teste.
- Watchtowers ajudam contra fechamento malicioso, não substituem backup.
- Macaroons: use o mínimo privilégio (`invoice` para criar, `readonly` para consultar); **nunca** `admin` na aplicação.
- O preimage é segredo; não logue, não exponha, não coloque em URL.

## Anti-padrões

- Vender LND compartilhado como "não-custodial".
- Aplicação com `admin.macaroon`.
- Nó sem liquidez de entrada e sem monitoramento.
- Um único nó sem backup testado.
- Trocar engine sem camada de abstração e reescrever o PDV inteiro.

## Perguntas de diagnóstico

- Quem tem as chaves? Quem pode censurar?
- Uma loja ou várias? Precisa de isolamento?
- Qual o volume e a direção (receber/pagar)?
- Há plano de liquidez, backup e monitoramento?
