# PRISMA-3 — Fonte de Cadeia e Sincronização de Backend

## O que a spec define

Conexão, validação de estado e sincronização com a blockchain Bitcoin, garantindo que a aplicação determine o estado real da rede com soberania e abasteça os motores de liquidação.

## Modos

| Ícone | Modo | Descrição |
|---|---|---|
| 🛡️ | Sovereign Full | Bitcoin Core full/pruned com validação local completa (Zero-Trust), via ZMQ e RPC |
| 🪶 | Compact Light | Clientes leves com filtros compactos (BIP 157/158), como Neutrino |
| 🔎 | Indexed Gate | Camadas intermediárias (Esplora/Fulcrum) para consultas instantâneas e mempool |
| 📡 | Remote RPC / Provider Relay | Middleware para nós remotos de terceiros; baixa latência, sem sync local |

## Trade-offs

| Modo | Validação | Armazenamento | Privacidade | Complexidade |
|---|---|---|---|---|
| Sovereign Full | Total | Alto (centenas de GB; pruned reduz muito) | Alta | Média |
| Compact Light | Parcial (filtros) | Baixo | Média | Baixa |
| Indexed Gate | Depende do backend | Alto (índice) | Média | Média/Alta |
| Remote RPC | Nenhuma local | Baixo | Baixa (provedor vê consultas) | Baixa |

## Compatibilidade com engines

- LND e Eclair costumam operar com Bitcoin Core via RPC/ZMQ; LND também suporta Neutrino.
- CLN normalmente usa Bitcoin Core (`bcli`).
- Requisitos de `txindex`, pruning e indexação **variam por versão** — confira a documentação do engine antes de prometer uma configuração.
- Indexadores (Esplora/Fulcrum) não substituem a validação do Core; eles servem consultas.

## Prática recomendada (fora da spec)

- Para operação séria: Bitcoin Core full ou pruned + ZMQ/RPC local. Pruned é um bom equilíbrio para loja/nó pessoal.
- Indexador só se houver necessidade real (explorador, conciliação on-chain, carteira).
- Remote RPC é aceitável para protótipo/baixo risco; não é soberania.
- Planeje disco e tempo de sincronização inicial; monitore espaço livre.
- Faça backup da config e saiba restaurar; chain pode ressincronizar, canais não.

## Anti-padrões

- Neutrino + discurso "zero-trust".
- RPC remoto + discurso "valido tudo localmente".
- Ignorar que indexador consome disco/RAM e pode derrubar o nó.
- Rodar engine sem entender requisitos de indexação da versão.

## Perguntas de diagnóstico

- Preciso validar tudo ou aceito confiança parcial?
- Qual o disco disponível e o tempo aceitável de sync?
- Preciso de indexador para conciliação on-chain?
- Quem controla o nó remoto, se houver?
