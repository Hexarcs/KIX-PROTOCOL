# PRISMA-6 — Perfis de Execução de Faturas

## O que a spec define

Distribuição da carga computacional entre o terminal de ponto de venda (PDV) e o back-office, desacoplando a lógica do terminal da necessidade de recursos altos.

## Perfis

| Ícone | Perfil | Descrição |
|---|---|---|
| 🏋️ | PRISMA-6-HEAVY | Servidores `x86_64`/`ARM64` com Django/FastAPI, banco, nó completo e webhooks |
| 🔋 | PRISMA-6-HYBRID | Android POS (Termux/Sunmi), PWA/apps nativos, certificados locais, impressão via API |
| 🪫 | PRISMA-6-LIGHT | Microcontroladores (ESP32/STM32 com TFT): só interface visual |

## Arquitetura recomendada

- **HEAVY é a fonte da verdade**: estado do pedido, criação de invoice, assinatura/consulta ao engine, conciliação, webhook.
- **Terminais são sem estado**: pedem uma cobrança, renderizam o QR, aguardam o resultado.
- **LIGHT não faz criptografia**: envia o valor por MQTT/WebSocket/REST, desenha o QR retornado e espera callback.
- **HYBRID** cobre o meio: roda PWA/app, guarda certificados e imprime, mas delega o pagamento ao HEAVY.

## Transporte e resiliência

- REST para request/response; WebSocket para push; MQTT para terminais leves/instáveis.
- Polling com timeout e backoff é aceitável; prefira push quando houver.
- Idempotência por ID de pedido e por payment hash.
- Fila offline no terminal: registra o pedido, sincroniza depois; não perde a venda.
- Contingência (eCash/voucher) só com regra de negócio explícita e reconciliação posterior.
- Autentique terminais (mTLS, token por dispositivo) e o webhook.

## Fluxo de exemplo (PDV)

1. Terminal (HYBRID/LIGHT) envia `{pedido, valor}` ao HEAVY.
2. HEAVY cria a invoice no engine (PRISMA-4) no formato escolhido (PRISMA-5); guarda `r_hash ↔ pedido`.
3. HEAVY devolve `payment_request`/payload do QR.
4. Terminal renderiza o QR (ou imprime).
5. HEAVY escuta liquidação (`SubscribeInvoices`) e, ao liquidar, marca pago e dispara webhook/evento.
6. Terminal recebe push ou faz polling e fecha a venda.

## Prática recomendada (fora da spec)

- Um único HEAVY por loja/rede; não duplique lógica de cobrança no terminal.
- TFT/MCU: mantenha firmware simples e atualizável; valide o QR renderizado.
- Android: cuidado com economia de bateria matando o processo; use foreground service/PWA confiável.
- Defina timeout de exibição do QR e mensagem clara de expiração.
- Registre métricas: invoices criadas, liquidadas, expiradas, tempo médio de pagamento.

## Anti-padrões

- Processar criptografia no microcontrolador.
- Terminal guardar chaves ou macaroons.
- Webhook sem autenticação e sem idempotência.
- Long-poll síncrono sem timeout.
- Expor o engine diretamente ao terminal.
- Duplicar regra de preço em cada terminal.

## Perguntas de diagnóstico

- Quem é a fonte da verdade do pedido?
- Quantos terminais e de que tipo?
- A rede cai? Qual o plano offline?
- Como o terminal é autenticado e atualizado?
