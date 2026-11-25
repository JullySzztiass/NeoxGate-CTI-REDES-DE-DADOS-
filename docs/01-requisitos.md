# Requisitos e status

A matriz abaixo relaciona os requisitos do briefing ao estado registrado em **10/11/2026** e aponta onde consultar os detalhes.

| Símbolo | Significado |
|---------|-------------|
| :white_check_mark: | Atendido |
| :construction: | Parcial ou em andamento |
| :hourglass_flowing_sand: | Pendente |
| :grey_question: | Sem registro na documentação |

## Matriz

| Requisito | Situação | Onde |
|-----------|----------|------|
| VLANs 3 (servidores), 5 (colaboradores), 8 (TI) e 10 (IoT) | :white_check_mark: | [Switches](05-switches.md#switch-da-matriz) |
| Firewall com controle de navegação | :construction: Políticas de saída criadas; servidores ainda usam placas temporárias de internet | [Firewalls](06-firewalls.md#políticas-de-firewall) |
| VLAN 5 acessa as aplicações; VLAN 8 acessa servidores por portas definidas e os colaboradores | :white_check_mark: Políticas 4 a 7 | [Firewalls](06-firewalls.md#políticas-de-firewall) |
| Somente equipamentos autorizados acessam a rede | :grey_question: | |
| Acesso remoto ao servidor Windows com SSO e dupla autenticação | :hourglass_flowing_sand: | [Pendências](10-pendencias.md) |
| VPN firewall ↔ nuvem (banco de dados) | :hourglass_flowing_sand: Faixas definidas, sem configuração | [VPN](07-vpn.md#desenho) |
| VPN matriz ↔ filial | :construction: Matriz configurada, túnel não estabeleceu | [VPN](07-vpn.md#estado-em-10112026) |
| PCs da matriz isolados dos PCs da filial | :white_check_mark: Nenhuma política liga os dois lados | [Segurança](09-seguranca.md#controles-aplicados) |

## Filial

| Requisito | Situação | Onde |
|-----------|----------|------|
| Escritório de 5 pessoas, só vendas | :construction: Faixas e firewall definidos | [Arquitetura](02-arquitetura.md) |
| Somente equipamentos autorizados acessam a rede | :grey_question: | |
| Firewall com controle de navegação | :construction: `lan1` configurada, `lan2` (WAN) sem link | [Firewalls](06-firewalls.md#firewall-da-filial) |
| Switch de acesso | :construction: Configuração inicial provisória | [Switches](05-switches.md#switch-da-filial) |
| VPN para a nuvem e para a matriz | :hourglass_flowing_sand: | [VPN](07-vpn.md#desenho) |
| Navegação pela saída do firewall da matriz | :hourglass_flowing_sand: | [Pendências](10-pendencias.md) |

## Ambos os escritórios

| Requisito | Situação | Onde |
|-----------|----------|------|
| Sensores de presença, luz e distância em rede apartada (VLAN 10) | :construction: Matriz atendida (emulado); filial pendente | [Monitoramento e IoT](04-monitoramento-iot.md) |
| Alarme acionado em matriz e filial quando o sensor foge do padrão | :construction: Matriz: alerta no Zabbix; filial pendente | [Monitoramento e IoT](04-monitoramento-iot.md#alertas-triggers) |

## Rede VPN

| Requisito | Situação | Onde |
|-----------|----------|------|
| Anel entre matriz, filial e nuvem, mantendo a comunicação se um link falhar | :hourglass_flowing_sand: Depende do firewall do meio e da filial | [VPN](07-vpn.md) |

## Aplicação

| Requisito | Situação | Onde |
|-----------|----------|------|
| Servidor de aplicação na matriz, acessado pelos funcionários | :white_check_mark: Apache em `/clientes`, liberado para as VLANs 5 e 8 | [Servidores](03-servidores.md#aplicação-de-cadastro) |
| Cadastro de clientes em banco de dados | :white_check_mark: Com banco local | [Servidores](03-servidores.md#aplicação-de-cadastro) |
| Banco hospedado na nuvem | :hourglass_flowing_sand: | [Pendências](10-pendencias.md) |
| Sensores monitorados e logs gerenciados | :white_check_mark: Na matriz | [Monitoramento e IoT](04-monitoramento-iot.md) |
