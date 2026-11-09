# PRISMA-2 — Topologia, Implantação e Exposição de Rede

## O que a spec define

Diretrizes de conteinerização, isolamento de processos e exposição: como os microsserviços KIX são empacotados, orquestrados e disponibilizados — de **Docker/Podman** a **bare-metal**.

## Modos

| Ícone | Modo | Descrição |
|---|---|---|
| 🧅 | Sovereign Darknet | Endereços Tor `.onion` v3 isolados em contêineres; soberania e privacidade |
| 🚇 | Secure Tunnel | Orquestração dinâmica via Cloudflare Tunnel ou VPN em malha (Tailscale), baixa latência |
| 🌍 | Public Clearnet | Execução nativa/host-network exposta por IPv4/IPv6 com proxy reverso (Nginx/Caddy) e TLS automático |
| 📡 | Mesh & Offline-Resilient | LoRaWAN ou redes p2p locais (Wi-Fi/Bluetooth) para locais sem internet |

## Portas e serviços que importam

- **LND P2P**: 9735 (precisa ser alcançável para peers; pode ser só Tor).
- **LND gRPC**: 10009 e **REST**: 8080 — **nunca** expostos publicamente.
- **CLN**: 9735 (P2P); API via `lightning-cli`/plugin, protegida.
- **Bitcoin Core P2P**: 8333; **RPC**: 8332 — nunca público.
- **Checkout/web**: 443 atrás de proxy reverso com TLS.

## Critérios de decisão

1. **Privacidade**: Tor esconde o IP, mas aumenta latência e complexidade; clearnet expõe origem.
2. **Alcance**: clearnet é o mais simples para clientes; Tor exige carteira compatível.
3. **NAT/CGNAT**: sem IP público, use túnel (Tailscale/Cloudflare) ou Tor.
4. **Operação**: túnel gerenciado reduz trabalho, mas adiciona dependência de terceiro.
5. **Ataque**: clearnet exige firewall, rate limit, WAF e plano de DDoS.
6. **Legal/negócio**: um checkout público precisa de HTTPS confiável e domínio estável.

## Prática recomendada (fora da spec)

- Proxy reverso (Nginx/Caddy) na frente de qualquer serviço HTTP; TLS com Let's Encrypt.
- Administração por VPN (Tailscale/WireGuard) ou `.onion`, nunca por RPC aberto.
- Firewall default-deny; exponha apenas 9735 (se P2P clearnet) e 443.
- Cloudflare Tunnel termina TLS na borda: entenda quem vê o tráfego antes de usar.
- Containers com usuário não-root, limites de recurso e volumes explícitos.
- Para loja sem IP público: Tailscale para admin + Tor para P2P é um bom equilíbrio.

## Anti-padrões

- Expor 8080/10009/8332 na internet.
- Administrar o nó por clearnet sem VPN e sem MFA.
- Usar TLS autoassinado em checkout público.
- Achar que Tor no P2P protege o checkout HTTP, que continua em clearnet.
- Ignorar que o provedor de túnel vê metadados.

## Perguntas de diagnóstico

- O nó tem IP público ou está atrás de NAT/CGNAT?
- Quem precisa alcançar o quê: peers, clientes, administrador?
- Privacidade é requisito real ou discurso? Qual o custo de latência aceitável?
- Há domínio, TLS e plano de DDoS para o checkout?
