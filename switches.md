# 🔀 Switches

[⬅ Voltar ao README](../README.md)

## SW-MATRIZ

Cisco WS-C2960S-24TD-L com VLANs 3, 5, 8 e 10.

| Porta | Função | Configuração |
|---|---|---|
| Gi4/0/1 | Firewall | Trunk |
| Gi4/0/2 | ESXi | Trunk, VLAN 3 nativa e VLAN 10 |
| Gi4/0/3 | Colaboradores | VLAN 5 |
| Gi4/0/4 | TI | VLAN 8 |
| Gi4/0/5 | IoT | VLAN 10 |

## SW-FILIAL

Cisco Catalyst 2960-S com configuração inicial provisória.

- Hostname: `SW-FILIAL-CTI`
- VLAN 10 criada como `IOT2`
- Portas Gi1/0/6 a Gi1/0/48 desativadas.
- Função das portas Gi1/0/1 a Gi1/0/5 ainda pendente.
