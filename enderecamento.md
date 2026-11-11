# 🌐 Endereçamento da rede

[⬅ Voltar ao README](../README.md)

## Matriz

| Elemento | Endereço |
|---|---|
| VLAN 3 - Servidores | 10.0.3.0/24 |
| VLAN 5 - Colaboradores | 10.0.5.0/24 |
| VLAN 8 - TI | 10.0.8.0/24 |
| VLAN 10 - IoT | 10.0.10.0/24 |
| Firewall da matriz | 192.168.100.10 |

## Filial

| Elemento | Endereço |
|---|---|
| VLAN 10 - IoT | 10.1.10.0/24 |
| PCs | 10.1.5.0/24 |
| Firewall da filial | 192.168.100.20 |

## Interconexões

| Elemento | Endereço |
|---|---|
| Firewall do meio | 192.168.100.1 |
| Banco de dados / VPC | 10.1.1.0/24 |
| Túnel filial ↔ meio | 172.31.0.0/30 |
| Túnel meio ↔ matriz | 172.31.0.12/30 |
| Túneis matriz ↔ nuvem | 169.254.10.0/30 e 169.254.10.4/30 |
| Túneis filial ↔ nuvem | 169.254.11.0/30 e 169.254.11.4/30 |

## Máquinas

| Máquina | IP | Rede |
|---|---|---|
| SRV-WIN | 10.0.3.10 | VLAN 3 |
| SRV-LINUX | 10.0.3.20 | VLAN 3 |
| IoT matriz | 10.0.10.10 | VLAN 10 |
| ESXi | 172.16.31.38 | Rede física |
| iDRAC | 172.16.9.4 | Rede física |
