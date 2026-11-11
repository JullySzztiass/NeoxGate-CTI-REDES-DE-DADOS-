# 🏗️ Arquitetura da solução

[⬅ Voltar ao README](../README.md)

## Visão geral

A solução é composta por matriz, meio, filial e nuvem.

| Local | Componentes | Função |
|---|---|---|
| Matriz | FortiGate 40F, switch Cisco, servidor Dell com ESXi, VMs e PCs | Rede principal |
| Meio | FortiGate-VM | Intermedia os túneis |
| Filial | Firewall, switch Cisco, PCs e IoT | Escritório local |
| Nuvem | Banco de dados em AWS (VPC) | Dados centralizados |

A matriz utiliza VLANs para separar servidores, colaboradores, TI e IoT. O ambiente também possui monitoramento centralizado, syslog e comunicação por MQTT.
