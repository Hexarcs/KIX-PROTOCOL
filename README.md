# ⚡ KIX: Uma Especificação Estruturada para Infraestruturas de Pagamento Lightning

<p align="center">
  <img src="https://img.shields.io/badge/status-rascunho-yellow?style=flat-square" alt="Status">
  <img src="https://img.shields.io/badge/licença-open--source-blue?style=flat-square" alt="Licença">
  <img src="https://img.shields.io/badge/rede-Lightning-792DE4?style=flat-square" alt="Lightning">
  <img src="https://img.shields.io/badge/bitcoin-native-F7931A?style=flat-square&logo=bitcoin&logoColor=white" alt="Bitcoin">
  <img src="https://img.shields.io/badge/arquitetura-P.R.I.S.M.A.-informational?style=flat-square" alt="PRISMA">
</p>

<p align="center">
  <b>🔓 Soberano · 🕸️ Descentralizado · 🔐 Não-Custodial · 🧩 Modular</b>
</p>

<p align="center">
  🌐 <b>Português (BR)</b> · <a href="README_EN.md">English</a>
</p>

<p align="center">
  📄 Especificação completa em PDF:
  <a href="KIX%20spec%20protocol_pt-br.pdf">Português (BR)</a> ·
  <a href="KIX%20spec%20protocol.pdf">English</a>
</p>

---

## 📖 Resumo

A complexidade de implementação e comunicação em redes de pagamento de segunda camada frequentemente limita sua adoção em larga escala. Propomos o **KIX**, um protocolo de especificação open-source projetado para simplificar a carga cognitiva ao fragmentar a arquitetura da Lightning Network em etapas modulares e padronizadas. Em vez de exigir que cada operador descreva exaustivamente sua própria pilha de hardware e software para interagir com o ecossistema, o protocolo padroniza a comunicação técnica, permitindo que a comunidade se refira a camadas arquiteturais universais. O modelo, fundamentado no **KIX Core**, é estritamente focado na implementação prática da rede, oferecendo ao usuário a flexibilidade de se abstrair da complexidade técnica subjacente, caso deseje. Como uma especificação baseada em elementos open-source, o KIX alinha os incentivos estruturais necessários para escalar a infraestrutura de pagamento de forma soberana, descentralizada e não-custodial.

---

## 📑 Sumário

- [1. Introdução](#-1-introdução)
- [2. PRISMA-1: Infraestrutura de Execução e Perfis de Host](#-2-prisma-1-infraestrutura-de-execução-e-perfis-de-host)
- [3. PRISMA-2: Topologia, Implantação e Exposição de Rede](#-3-prisma-2-topologia-implantação-e-exposição-de-rede)
- [4. PRISMA-3: Fonte de Cadeia e Sincronização de Backend](#-4-prisma-3-fonte-de-cadeia-e-sincronização-de-backend)
- [5. PRISMA-4: Abstração do Motor Lightning](#-5-prisma-4-abstração-do-motor-lightning)
- [6. PRISMA-5: Codificadores de Faturas Multi-Formato](#-6-prisma-5-codificadores-de-faturas-multi-formato)
- [7. PRISMA-6: Perfis de Execução de Faturas](#-7-prisma-6-perfis-de-execução-de-faturas)

---

## 🧭 1. Introdução

> **P.R.I.S.M.A.** — *Protocol Routing & Infrastructure Specification for Merchant Applications*
> (Especificação de Roteamento e Infraestrutura de Protocolo para Aplicações Comerciais)

A operação soberana de um nó completo do Bitcoin, aliada a implementações tradicionais da Lightning Network — como **CLN**, **LND** ou **Eclair** —, frequentemente representa uma barreira técnica intimidadora para comerciantes locais e pessoas comuns. Ao longo dos anos, a comunidade concentrou-se no desenvolvimento de diversas tecnologias e abstrações para reduzir essa fricção inicial, com o objetivo constante de mitigar a complexidade e diminuir drasticamente os requisitos computacionais necessários para utilizar a rede de pagamentos no dia a dia.

Apesar desses avanços estruturais, observa-se que até mesmo desenvolvedores de software e engenheiros imersos no ecossistema Lightning muitas vezes desconhecem a vasta diversidade de hardware que pode ser empregada na implementação prática da rede. A padronização arquitetural proposta pelo protocolo KIX facilita ativamente o desenvolvimento do ecossistema ao criar uma linguagem técnica comum, permitindo que a comunidade compreenda capacidades operacionais que vão desde servidores corporativos em nuvem até microcontroladores de baixíssimo custo e consumo energético.

Além disso, a especificação desempenha um papel crucial na prevenção de uma *"visão em túnel"* entre os implementadores em relação às topologias de comunicação. O KIX deixa explícito que diversas abordagens de exposição e de rede podem ser adotadas — e até executadas em paralelo —, definindo com precisão em qual estágio da arquitetura de um nó o sistema pode ser escalado horizontalmente com ferramentas de conteinerização como o **Docker** e seus derivados.

Por fim, a organização do protocolo localiza arquiteturalmente o ecossistema de software de pagamento. Ela determina exatamente onde se posicionam as soluções de front-end de ponto de venda (PDV) e os ecossistemas de gerenciamento de carteiras — como **LNbits** ou interfaces via **NWC** (Nostr Wallet Connect). Em essência, a especificação mapeia como os módulos de nível superior se comunicam, por meio de requisições de API, com os motores inferiores da pilha, garantindo que o processamento pesado e o roteamento de faturas sejam abstraídos e delegados às camadas apropriadas, libertando o usuário final da complexidade criptográfica e computacional subjacente.

---

## 🖥️ 2. PRISMA-1: Infraestrutura de Execução e Perfis de Host

O PRISMA-1 estabelece as fundações físicas e operacionais do protocolo KIX, definindo as diretrizes e a especificação de ambientes de hardware e sistemas operacionais (com suporte a ambientes **Linux**, **Android** e **Windows**) capazes de sustentar uma implementação em conformidade com a arquitetura **P.R.I.S.M.A.** A escolha do sistema operacional e da plataforma de execução impacta profundamente o comportamento dos demais módulos do protocolo, definindo desde a disponibilidade de motores de roteamento Lightning nativos até os métodos de exposição de rede e de armazenamento. O objetivo fundamental deste módulo é fornecer um modelo de referência soberano para a camada de execução.

### 🧱 2.1 Arquiteturas e Requisitos de Instrução

O modelo orienta o uso de ambientes de **64 bits** para garantir a interoperabilidade do conjunto de ferramentas (toolchain). Embora a filosofia seja multi-arquitetura (`x86_64`, `ARM64` e, eventualmente, `RISC-V`), o padrão de 64 bits é fundamental para manter a estabilidade dos componentes críticos.

### 📊 2.2 Dimensionamento e Impacto de Recursos

| Perfil | Arquitetura | RAM típica | Caso de uso |
|---|---|---|---|
| 🪶 **Restrito / Embarcado** | ARM64 / SBCs | 1 GB – 4 GB | NanoPi, Orange Pi — estratégias de sincronização leves |
| 🏗️ **Expandido** | x86_64 / Servidores e Nuvem | 8 GB+ | Motores Lightning completos, indexadores, back-office |

### 🧩 2.3 Composição Modular por S.H.A.R.D.s

> **S.H.A.R.D.** = *Software Hardware Automated Routing Division*
> Um S.H.A.R.D. de Infraestrutura representa a unidade física e operacional básica do ambiente host.

- 📱 **Micro-Embarcado** — Terminal Android (Sunmi V2) ou SBC ultracompacto. Focado em chamadas de API via conexão remota.
- 🏪 **Edge / Chão de Loja** — SBC (NanoPi, Raspberry Pi 4) para servidor local de balcão.
- 🖥️ **Desktop Autônomo** — PC/Mini-PC dedicado para executar um nó local completo e microsserviços de PDV.
- ☁️ **Nó Cloud / Empresarial** — Instância virtualizada atuando como servidor centralizador (Cluster / Multi-Tenant).

---

## 🌐 3. PRISMA-2: Topologia, Implantação e Exposição de Rede

O PRISMA-2 define as diretrizes para conteinerização, isolamento de processos e estratégias de exposição de rede. O objetivo é padronizar como os microsserviços KIX são empacotados, orquestrados e disponibilizados, cobrindo desde ambientes de conteinerização (**Docker**, **Podman**) até a execução direta (**bare-metal**).

### 🛰️ 3.1 Composição Modular por S.H.A.R.D.s de Implantação e Exposição

| Ícone | Modo | Descrição |
|---|---|---|
| 🧅 | **Darknet Soberana** | Endereços Tor `.onion` v3 isolados em contêineres, focados em soberania total e privacidade. |
| 🚇 | **Túnel Seguro** | Orquestração dinâmica expondo serviços via Cloudflare Tunnel ou VPN em malha (Tailscale) com baixa latência. |
| 🌍 | **Clearnet Pública** | Execução nativa ou em rede do host exposta via IPv4/IPv6 direto com proxy reverso (Nginx/Caddy) e TLS automático. |
| 📡 | **Malha e Resiliente Offline** | Transmissão via rádio LoRaWAN ou redes p2p locais (Wi-Fi/Bluetooth) para locais sem cobertura de internet. |

---

## ⛓️ 4. PRISMA-3: Fonte de Cadeia e Sincronização de Backend

O PRISMA-3 especifica as diretrizes para conexão, validação de estado e sincronização com a blockchain do Bitcoin, garantindo que a aplicação determine o estado real da rede com soberania, abastecendo os motores de liquidação.

### 🔄 4.1 Mecanismos de Sincronização e Fontes de Dados

| Ícone | Modo | Descrição |
|---|---|---|
| 🛡️ | **Full Soberano** | Execução direta do Bitcoin Core (Nó Full/Pruned) com validação local completa (Zero-Trust) acoplado via ZMQ e RPC. |
| 🪶 | **Light Compacto** | Clientes leves via filtros compactos (BIP 157/158), como o Neutrino, com eficiência extrema de armazenamento. |
| 🔎 | **Gate Indexado** | Camadas intermediárias (Esplora / Fulcrum) para consultas instantâneas e suporte a mempool. |
| 📡 | **RPC Remoto / Relay de Provedor** | Middleware conectando o PDV a nós remotos gerenciados por terceiros, focado em baixa latência e sem sincronização local. |

---

## ⚙️ 5. PRISMA-4: Abstração do Motor Lightning

O PRISMA-4 especifica a camada de abstração e comutação entre motores de liquidação, tornando as interfaces de PDV completamente agnósticas ao backend de pagamento. Permite alternar dinamicamente entre motores executados localmente ou hospedados por terceiros.

### 🔌 5.1 Composição Modular por S.H.A.R.D.s de Motor Lightning

| Ícone | Modo | Descrição |
|---|---|---|
| 🏢 | **Enterprise Nativo** | Motores diretos como LND ou Core Lightning para nós soberanos de alto volume. |
| 🧩 | **Motor Modular** | LNbits ou Alby Hub locais, ideais para ecossistemas multi-tenant e isolamento por loja. |
| 🤖 | **Servidor Autônomo** | Motores como o Phoenixd, focados em gerenciamento automático de liquidez via splicing. |
| 📱 | **Remoto Delegado** | Carteiras remotas via Nostr Wallet Connect (NWC), conectadas como cliente sem infraestrutura de nó local. |
| 🧰 | **SDK Embarcado** | Provedores de API ou backends em nuvem baseados em LDK (Lightning Development Kit), com chaves mantidas pelo cliente. |

---

## 🧾 6. PRISMA-5: Codificadores de Faturas Multi-Formato

O PRISMA-5 garante interoperabilidade máxima no PDV ao gerar e converter dinamicamente formatos nativos de cobrança da rede e esquemas proprietários de contingência.

### 🔀 6.1 Formatos Padronizados e Estratégias de Fallback

| Ícone | Modo | Descrição |
|---|---|---|
| ⚡ | **Padrão Nativo** | Geração nativa de faturas BOLT 11 (uso único) e suporte a Offers (BOLT 12). |
| 🔗 | **Universal Unificado** | QR Codes BIP 21 agrupando On-Chain e Lightning, integrados à resolução LNURL-Pay para cobrir qualquer carteira de cliente. |
| 🪙 | **Contingência e Tokenizado** | Conversão para Tokens eCash (Cashu/Fedimint) ou vouchers de crédito locais durante interrupções de rede. Emite faturas contingentes com liquidação enfileirada para compensação posterior, sem parar o caixa. |

---

## 🧮 7. PRISMA-6: Perfis de Execução de Faturas

O PRISMA-6 especifica a distribuição da carga computacional entre o terminal de ponto de venda (PDV) e a infraestrutura de back-office, desacoplando a lógica do terminal da necessidade de altos recursos.

### ⚖️ 7.1 Distribuição de Carga Computacional

| Ícone | Perfil | Descrição |
|---|---|---|
| 🏋️ | **PRISMA-6-HEAVY** | Servidores (`x86_64`/`ARM64`) executando frameworks web (Django, FastAPI), gerenciando bancos de dados, nós Lightning completos e webhooks de e-commerce síncronos. |
| 🔋 | **PRISMA-6-HYBRID** | Dispositivos PDV Android portáteis (Termux/Sunmi) capazes de executar PWAs e aplicações nativas, gerenciando certificados locais e imprimindo recibos via API em nuvem. |
| 🪫 | **PRISMA-6-LIGHT** | Microcontroladores (ESP32, STM32 com displays TFT). Atuam puramente como interface visual. Transmitem o valor do pedido via MQTT, WebSockets ou REST para o servidor HEAVY, renderizam o QR Code retornado e aguardam o callback sem processar criptografia localmente. |

---

## 🗺️ Visão Geral da Arquitetura

```text
┌───────────────────────────────────────────────────────────────┐
│                    🧾 CAMADA POS / CARTEIRA                    │
│       (PDV, LNbits, NWC, PWAs, Dashboards Web, etc.)          │
└───────────────────────────┬───────────────────────────────────┘
                            │  REST / WebSocket / MQTT
┌───────────────────────────▼───────────────────────────────────┐
│              🧮 PRISMA-6 · Execução de Faturas                 │
│        HEAVY  │  HYBRID  │  LIGHT                              │
└───────────────────────────┬───────────────────────────────────┘
                            │
┌───────────────────────────▼───────────────────────────────────┐
│         🧾 PRISMA-5 · Codificadores Multi-Formato              │
│     BOLT 11 · BOLT 12 · BIP 21 · LNURL · eCash                 │
└───────────────────────────┬───────────────────────────────────┘
                            │
┌───────────────────────────▼───────────────────────────────────┐
│        ⚙️ PRISMA-4 · Abstração do Motor Lightning              │
│    LND · CLN · Eclair · Phoenixd · LNbits · LDK · NWC          │
└───────────────────────────┬───────────────────────────────────┘
                            │
┌───────────────────────────▼───────────────────────────────────┐
│        ⛓️ PRISMA-3 · Fonte de Cadeia & Sincronização           │
│   Bitcoin Core · Neutrino · Esplora · Fulcrum · RPC Remoto     │
└───────────────────────────┬───────────────────────────────────┘
                            │
┌───────────────────────────▼───────────────────────────────────┐
│          🌐 PRISMA-2 · Topologia & Exposição                   │
│   Tor · Cloudflare Tunnel · Tailscale · Clearnet · LoRaWAN     │
└───────────────────────────┬───────────────────────────────────┘
                            │
┌───────────────────────────▼───────────────────────────────────┐
│          🖥️ PRISMA-1 · Infraestrutura & Host                   │
│    SBCs · Desktops · Cloud · Embarcados · Multi-Arquitetura    │
└───────────────────────────────────────────────────────────────┘
```
