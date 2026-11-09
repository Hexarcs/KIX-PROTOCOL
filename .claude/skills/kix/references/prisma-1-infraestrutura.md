# PRISMA-1 — Infraestrutura de Execução e Perfis de Host

## O que a spec define

Camada física e operacional: hardware, sistema operacional e o perfil de host capaz de sustentar a implementação. A spec cita **Linux, Android e Windows**, ambientes de **64 bits** e filosofia multi-arquitetura (`x86_64`, `ARM64` e, eventualmente, `RISC-V`).

## Perfis

| Perfil | Arquitetura | RAM típica | Caso de uso |
|---|---|---|---|
| 🪶 Restrito / Embarcado | ARM64 / SBCs | 1 GB – 4 GB | NanoPi, Orange Pi — sincronização leve |
| 🏗️ Expandido | x86_64 / servidores e cloud | 8 GB+ | Engines completos, indexadores, back-office |

## S.H.A.R.D.s de infraestrutura

> **S.H.A.R.D.** = *Software Hardware Automated Routing Division* — unidade física e operacional básica do ambiente host.

| S.H.A.R.D. | Exemplo | Papel |
|---|---|---|
| 📱 Micro-Embedded | Terminal Android (Sunmi V2), SBC ultracompacto | Chamadas de API via conexão remota |
| 🏪 Edge / Shop Floor | NanoPi, Raspberry Pi 4 | Servidor local de balcão |
| 🖥️ Standalone Desktop | PC/Mini-PC dedicado | Nó local completo + microsserviços de PDV |
| ☁️ Cloud / Enterprise | Instância virtualizada | Servidor centralizador (cluster/multi-tenant) |

## Critérios de decisão

1. **Carga real**: nó Bitcoin + engine + indexador + banco + aplicação. Indexadores e banco puxam disco e RAM muito mais que o LND.
2. **Confiabilidade**: SSD (não microSD) para chain/DB; UPS; refrigeração; fonte decente.
3. **Acesso remoto**: console serial, SSH, VPN de administração, atualização sem tocar no hardware.
4. **Ambiente**: calor, poeira, oscilação de energia, roubo — típicos de comércio.
5. **Soberania**: cloud só é soberana se você controla a conta, a criptografia e o acesso; caso contrário, é custódia de infraestrutura.
6. **Arquitetura**: `x86_64` para stacks pesadas/indexadores; `ARM64` para perfis leves; 32 bits não é suportado pela filosofia.

## Prática recomendada (fora da spec)

- Prefira SSD e `x86_64` quando houver indexador (Esplora/Fulcrum) ou banco de dados.
- `ARM64` funciona bem para LND/CLN + Bitcoin Core pruned em loja pequena.
- Android/Termux tem limites de segundo plano, térmica e bateria; não trate como servidor 24/7 sem cuidados.
- Tenha plano de backup e de restauração testado — hardware sem backup não é soberania, é risco.
- Monitore disco, temperatura e energia; nó que enche o disco para de funcionar silenciosamente.

## Anti-padrões

- Rodar stack completa em microSD.
- Um único SBC sem UPS, sem backup e sem monitoramento num caixa de loja.
- Dizer "soberano" usando VPS cujo provedor pode desligar a máquina e cujos discos você não controla.
- Ignorar requisitos de indexação/pruning do engine escolhido — verifique a documentação da versão.

## Perguntas de diagnóstico

- Onde o nó vai ficar fisicamente? Há energia estável e rede?
- Qual o orçamento e quem opera/manutenção?
- Preciso de indexador/banco local ou só de engine + chain?
- Perder o nó por quanto tempo é aceitável? Existe backup testado?
