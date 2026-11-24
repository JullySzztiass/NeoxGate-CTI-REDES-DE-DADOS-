# Requisitos e status

O desafio pede um ambiente que reproduza uma empresa real, com nuvem, roteamento, resiliência de comunicação, segurança, IoT, desenvolvimento e monitoramento. A tabela cruza cada requisito do briefing com a situação atual (base: documentação de 10/11/2026).

**Legenda:** Atendido · Parcial · Pendente · Sem registro (não aparece na documentação recebida)

## Matriz

| Requisito | Situação | Onde |
|-----------|----------|------|
| VLANs 3 (servidores), 5 (colaboradores), 8 (TI) e 10 (IoT) | Atendido | [Rede](rede-firewall-vpn.md#switch-da-matriz) |
| Firewall com controle de navegação | Parcial: políticas de saída criadas; servidores ainda usam placas temporárias de internet | [Rede](rede-firewall-vpn.md#políticas-do-firewall-da-matriz) |
| VLAN 5 acessa as aplicações; VLAN 8 acessa servidores por portas definidas e os colaboradores | Atendido no firewall (políticas 4 a 7) | [Rede](rede-firewall-vpn.md#políticas-do-firewall-da-matriz) |
| Somente equipamentos autorizados acessam a rede | Sem registro | |
| Acesso remoto ao servidor Windows com SSO e dupla autenticação | Pendente | [Pendências](pendencias.md) |
| VPN firewall ↔ nuvem (banco de dados) | Pendente: faixas definidas, sem configuração | [Arquitetura](arquitetura.md#endereçamento) |
| VPN matriz ↔ filial | Parcial: matriz configurada, túnel não estabeleceu | [Rede](rede-firewall-vpn.md#vpn) |
| PCs da matriz isolados dos PCs da filial | Atendido: nenhuma política liga os dois lados | [Rede](rede-firewall-vpn.md#vpn) |

## Filial

| Requisito | Situação | Onde |
|-----------|----------|------|
| Escritório de 5 pessoas, só vendas | Parcial: faixas e firewall definidos | [Arquitetura](arquitetura.md) |
| Somente equipamentos autorizados acessam a rede | Sem registro | |
| Firewall com controle de navegação | Parcial: `lan1` configurada, `lan2` (WAN) sem link | [Rede](rede-firewall-vpn.md#filial) |
| VPN para a nuvem e para a matriz | Pendente | [Rede](rede-firewall-vpn.md#filial) |
| Navegação pela saída do firewall da matriz | Pendente | [Pendências](pendencias.md) |

## Ambos os escritórios

| Requisito | Situação | Onde |
|-----------|----------|------|
| Sensores de presença, luz e distância em rede apartada (VLAN 10) | Matriz atendido (emulado); filial pendente | [Monitoramento e IoT](monitoramento-iot.md) |
| Alarme acionado em matriz e filial quando o sensor foge do padrão | Matriz: alerta no Zabbix; filial pendente | [Monitoramento e IoT](monitoramento-iot.md#alertas-triggers) |

## Rede VPN

| Requisito | Situação |
|-----------|----------|
| Anel entre matriz, filial e nuvem, mantendo a comunicação se um link falhar | Pendente: depende do firewall do meio e da filial |

## Aplicação

| Requisito | Situação | Onde |
|-----------|----------|------|
| Servidor de aplicação na matriz, acessado pelos funcionários | Atendido: Apache em `/clientes`, liberado para as VLANs 5 e 8 | [Servidores](servidores.md#aplicação-de-cadastro) |
| Cadastro de clientes em banco de dados | Atendido com banco local | [Servidores](servidores.md#aplicação-de-cadastro) |
| Banco hospedado na nuvem | Pendente | [Pendências](pendencias.md) |
| Sensores monitorados e logs gerenciados | Atendido na matriz | [Monitoramento e IoT](monitoramento-iot.md) |
