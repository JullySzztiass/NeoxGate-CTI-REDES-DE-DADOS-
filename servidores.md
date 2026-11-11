# 🖥️ Servidores e virtualização

[⬅ Voltar ao README](../README.md)

## Servidor físico e ESXi

As máquinas do projeto rodam em um Dell PowerEdge T440 com VMware ESXi 8.0 U3e.

| Item | Valor |
|---|---|
| Processador | Intel Xeon Bronze 3106 |
| Memória | 15,56 GB |
| Armazenamento | datastore1, 150,75 GB |
| RAID | RAID 0 |

## Máquinas virtuais

| VM | Sistema | Recursos | Rede |
|---|---|---|---|
| SRV-Windows25 - NeoxGate | Windows Server 2025 | 2 vCPU, 6 GB, 90 GB | VLAN 3 |
| SRV-LINUX - NeoxGate | Debian 12 | 2 GB | VLAN 3 |
| IoT - NeoxGate | Debian 12 | 2 vCPU, 2 GB, 8 GB | VLAN 10 |

## Serviços do SRV-LINUX

| Serviço | Função | Porta |
|---|---|---|
| Apache e PHP | Aplicação e interface do Zabbix | 80 e 443 |
| MariaDB | Bancos locais | 3306 |
| Mosquitto | Broker MQTT | 1883/TCP |
| Zabbix server | Monitoramento | 10051/TCP |
| Zabbix Agent 2 | Monitoramento local | 10050/TCP |
| rsyslog | Logs centralizados | 514 UDP/TCP |
| dnsmasq | DNS local | 53 |
| Grafana | Visualização | 3000/TCP |
