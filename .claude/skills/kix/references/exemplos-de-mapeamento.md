# Exemplos de mapeamento PRISMA

## Exemplo A — Nó pessoal soberano em SBC

| Camada | Decisão | Modo |
|---|---|---|
| PRISMA-1 | SBC ARM64, 4 GB RAM, SSD USB | Restrito/Embarcado · Edge |
| PRISMA-2 | Tor para P2P; admin via Tailscale | Sovereign Darknet + Secure Tunnel |
| PRISMA-3 | Bitcoin Core pruned + ZMQ/RPC | Sovereign Full (pruned) |
| PRISMA-4 | LND local | Native Enterprise |
| PRISMA-5 | BOLT 11; LNURL-Pay para receber | Native Standard + Unified Universal |
| PRISMA-6 | Celular/navegador como cliente | LIGHT |

Riscos: liquidez de entrada, backup SCB, térmica/energia do SBC.

## Exemplo B — Loja de bairro com PDV

| Camada | Decisão | Modo |
|---|---|---|
| PRISMA-1 | Mini-PC x86_64 no balcão + Android POS | Standalone Desktop + Micro-Embedded/HYBRID |
| PRISMA-2 | Clearnet para checkout + Tailscale para admin | Public Clearnet + Secure Tunnel |
| PRISMA-3 | Bitcoin Core pruned local | Sovereign Full |
| PRISMA-4 | LND único da loja | Native Enterprise |
| PRISMA-5 | BOLT 11 + LNURL + BIP 21 fallback | Native Standard + Unified Universal |
| PRISMA-6 | HEAVY no mini-PC; HYBRID no Android; LIGHT em TFT | HEAVY + HYBRID + LIGHT |

Fluxo: Android/TFT pede cobrança ao HEAVY; HEAVY cria invoice no LND; terminal mostra QR; HEAVY escuta `SubscribeInvoices`; ao liquidar, imprime/fecha a venda.

Riscos: queda de internet, liquidez de entrada, backup, expiração de invoice.

## Exemplo C — Marketplace com link de checkout e LND próprio

Premissa: plataforma SaaS oferece um link de pagamento; ao abrir, o backend chama o LND do operador. Quem opera o nó é **custodiante**.

| Camada | Decisão | Modo |
|---|---|---|
| PRISMA-1 | Cloud do operador (checkout + LND + Core) | Cloud/Enterprise |
| PRISMA-2 | Link público HTTPS; RPC privado | Public Clearnet + Secure Tunnel |
| PRISMA-3 | Bitcoin Core full/pruned no operador | Sovereign Full |
| PRISMA-4 | LND compartilhado | Native Enterprise (operador) / Delegated Remote (lojista) |
| PRISMA-5 | BOLT 11 por pedido; LNURL/BIP 21 | Native Standard + Unified Universal |
| PRISMA-6 | HEAVY no servidor; LIGHT no navegador; HYBRID no painel | HEAVY + LIGHT + HYBRID |

Fluxo com LND (backend privado, macaroon mínimo):

```
POST /v1/invoices {value, memo, expiry}   # ou lncli addinvoice
→ {r_hash, payment_request}
→ checkout renderiza QR lightning:lnbc...
→ GET /v1/invoices/subscribe               # ou lncli subscribeinvoices
→ state=SETTLED → pedido pago → webhook
```

Lacunas: custódia, multi-tenant (LND não tem subcontas), liquidez de entrada, repasse BRL/Pix, reembolsos, compliance. Pix não é PRISMA-5.

## Exemplo D — Auditoria de um nó existente

Checklist de saída:

1. **PRISMA-1**: hardware, disco, UPS, backup, acesso.
2. **PRISMA-2**: o que está exposto? RPC acessível? TLS? Firewall?
3. **PRISMA-3**: valida local? confia em indexador/RPC remoto?
4. **PRISMA-4**: quem tem chaves? liquidez de entrada/saída? backup de canais? macaroons?
5. **PRISMA-5**: formato do QR, expiração, conciliação, reembolso.
6. **PRISMA-6**: fonte da verdade, idempotência, offline, autenticação de terminal.
7. **Lacunas**: fiat/Pix, contabilidade, compliance, HA.

Formato: para cada camada, classifique **conforme / risco / lacuna** e proponha a menor correção.
