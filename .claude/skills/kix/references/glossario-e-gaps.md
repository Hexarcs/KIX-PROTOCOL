# Glossário e lacunas do KIX

## P.R.I.S.M.A.

Nos documentos oficiais (README e PDFs), o acrônimo **não é expansionado**. A expansão usual, encontrada como subtítulo em material do framework, é:

> **Protocol Routing & Infrastructure Specification for Merchant Applications**

Correspondência de iniciais (o "for" e o "&" não entram): **P**rotocol, **R**outing, **I**nfrastructure, **S**pecification, **M**erchant, **A**pplications. Apresente como expansão de iniciais, não como definição formal da spec.

## S.H.A.R.D.

> **Software Hardware Automated Routing Division** — unidade física/operacional básica do ambiente host.

## Termos

### Chain e sincronização

- **Full node**: valida blocos e transações localmente.
- **Pruned node**: valida tudo, mas descarta blocos antigos; guarda o UTXO set.
- **Neutrino / BIP 157-158**: cliente leve por filtros compactos de bloco.
- **Esplora / Fulcrum**: indexadores que servem consultas rápidas (endereços, mempool).
- **ZMQ / RPC**: interfaces do Bitcoin Core usadas pelo engine.
- **Zero-trust**: validação local completa, sem confiar em terceiros.

### Lightning

- **Canal**: capacidade compartilhada entre dois nós; tem liquidez de entrada e saída.
- **HTLC**: contrato hash-timelock que viabiliza o pagamento.
- **Preimage / payment hash**: segredo e seu hash; o preimage libera o HTLC.
- **Inbound/outbound liquidity**: capacidade de receber/enviar.
- **LSP**: provedor de serviços Lightning (abre canais, fornece liquidez).
- **JIT channel**: canal aberto no momento do pagamento.
- **Splicing**: redimensiona canal sem fechar (Phoenixd; suporte varia).
- **Rebalanceamento**: move liquidez entre canais.
- **SCB**: Static Channel Backup (LND).
- **Watchtower**: terceiro que vigia fechamentos maliciosos.
- **Macaroon**: credencial com permissões no LND.
- **LDK**: Lightning Development Kit, biblioteca embarcável.
- **NWC**: Nostr Wallet Connect (NIP-47), conecta app a carteira remota.

### Faturas e formatos

- **BOLT 11**: invoice de uso único, com valor e expiração.
- **BOLT 12**: Offers, invoices reutilizáveis e blinded paths; suporte varia.
- **BIP 21**: URI `bitcoin:` com parâmetro Lightning opcional.
- **LNURL-Pay**: endpoint bech32 que entrega uma invoice ao wallet.
- **Lightning Address**: `user@dominio`, alias para LNURL-Pay.
- **eCash (Cashu/Fedimint)**: tokens custodiais emitidos por mint/federação.

## Lacunas da spec (não cobertas pelos seis PRISMA)

1. **Fiat e off-ramp**: BRL, Pix, câmbio, tesouraria. O KIX termina no Lightning.
2. **Custódia e multi-tenant**: quem guarda chaves, como separar saldos por lojista.
3. **Liquidez**: LSP, canais, JIT, rebalanceamento, splicing.
4. **Reembolsos, disputas e chargeback**: fluxo e liquidez de saída.
5. **Compliance e KYC/AML**: regulação brasileira de cripto e obrigações do operador.
6. **Alta disponibilidade**: failover, replicação, restauração testada.
7. **Contabilidade e reconciliação**: pedido, pagamento, taxa, repasse, imposto.
8. **Precificação**: cotação BRL→sats, expiração e volatilidade.
9. **Monitoramento/observabilidade**: canais, peers, disco, liquidez, alertas.

## Extensões hipotéticas (proposta, não spec)

- **PRISMA-7 — Liquidez e Liquidação Fiat**: LSP, tesouraria, câmbio sats/BRL, Pix.
- **PRISMA-8 — Comércio e Reconciliação**: pedido, reembolso, webhook, contabilidade.

Deixe claro que essas camadas **não existem** no protocolo atual; são uma forma de nomear o que falta.
