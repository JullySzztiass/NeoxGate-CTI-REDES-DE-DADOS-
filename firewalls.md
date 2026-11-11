# 🔥 Firewalls

[⬅ Voltar ao README](../README.md)

## Matriz

| Item | Valor |
|---|---|
| Modelo | FortiGate 40F |
| FortiOS | 7.4.12 |
| VDOM | FGT-VDOM-MTZ-SENAI |
| Gerência | 192.168.100.10 |

### Interfaces

| Interface | Função | Endereço |
|---|---|---|
| lan1 | LAN | 192.168.200.10/24 |
| lan2 | WAN | 172.16.31.30/24 |
| a | - | 172.23.45.1/24 |

### Regras principais

- VLAN 5 acessa aplicações dos servidores.
- VLAN 8 possui acesso administrativo pré-definido.
- VLAN 10 permanece isolada e acessa apenas os serviços necessários.
- PCs da matriz e filial não se acessam diretamente.
- IoT utiliza MQTT e syslog para o SRV-LINUX.

## Meio

FortiGate-VM sem VDOM, utilizado para intermediar os túneis.

## Filial

O modelo exato do firewall ainda está pendente de confirmação. A `lan2` está sem link e sem IP.
