# Firewalls

Endereços em [02-arquitetura.md](02-arquitetura.md). Túneis e VPN em [07-vpn.md](07-vpn.md).

## Regras de acesso entre redes

Definidas pelo briefing e refletidas nas políticas abaixo.

| Origem | Pode acessar |
|--------|--------------|
| VLAN 5 (colaboradores) | As aplicações dos servidores |
| VLAN 8 (TI) | Os servidores por portas pré-definidas e os equipamentos dos colaboradores |
| VLAN 10 (IoT) | Apenas o necessário para o monitoramento (rede apartada) |
| PCs da matriz e da filial | Não se acessam |

## Firewall da matriz

| Item | Valor |
|------|-------|
| Modelo | Fortinet FortiGate 40F |
| Sistema operacional | FortiOS 7.4.12 (build 2902) |
| VDOM | `FGT-VDOM-MTZ-SENAI` |
| IP de gerência (projeto) | 192.168.100.10 |

**Interfaces** (coletadas em 10/11/2026)

| Interface | Função | Endereço | Estado |
|-----------|--------|----------|--------|
| `lan1` | LAN | 192.168.200.10/24 | up |
| `lan2` | WAN | 172.16.31.30/24 (DHCP) | up |
| `a` | — | 172.23.45.1/24 | down |

### Políticas de firewall

| ID | Nome | Origem → destino | Serviços |
|----|------|------------------|----------|
| 1 e 2 | Regras do VDOM Link (não editar) | any ↔ Root-T21 | ALL |
| 3 | Saida Internet Matriz | `lan1` → `lan2` | ALL |
| 4 | COLAB-para-SRVapps | VLAN 5 → VLAN 3 | HTTP, HTTPS, PING |
| 5 | TI-para-SRV-portas | VLAN 8 → VLAN 3 | SSH, RDP, HTTP, HTTPS, PING |
| 6 | TI-para-COLAB | VLAN 8 → VLAN 5 | ALL |
| 7 | TI-para-IOT | VLAN 8 → VLAN 10 | ALL |
| 8 | COLAB-para-Internet | VLAN 5 → `lan2` | HTTP, HTTPS, DNS, PING |
| 9 | TI-para-Internet | VLAN 8 → `lan2` | ALL |
| 10 | SRV-para-Internet | VLAN 3 → `lan2` | DNS, HTTP, HTTPS, PING |
| 11 | COLAB-TI-DNS-para-SRV | VLANs 5 e 8 → 10.0.3.20 | DNS |
| 12 | SRV-para-IOT | 10.0.3.20 e 10.0.3.10 → 10.0.10.10 | PING, HTTP, HTTPS |
| 13 | IOT-para-SRV-monitor | 10.0.10.10 → 10.0.3.20 | PING, MQTT-IOT, SYSLOG-UDP-514, SYSLOG-TCP-514 |

**Política 13.** Libera o monitoramento do IoT e continua restrita a esse par de hosts, com `logtraffic all`. O serviço `SYSLOG` padrão cobre só UDP 514, e o `RSH` (TCP 514 com porta de origem 512–1023) não serve para syslog TCP; por isso foram criados `SYSLOG-UDP-514` e `SYSLOG-TCP-514` ([configs/fortigate-matriz.txt](../configs/fortigate-matriz.txt)).

Verificação: `diagnose debug flow` filtrado por origem 10.0.10.10, destino 10.0.3.20 e porta 514 deve mostrar `Allowed by Policy-13`. Se o firewall libera e o servidor não recebe, confira no SRV-LINUX se o rsyslog escuta em 514 (`ss -lntu | grep 514`) e se o firewall do próprio servidor libera a porta.

**Pendências nas políticas**

- Nenhuma política usa a interface `VPN-MTZ-MEIO`. Sem políticas entre servidores/IoT da matriz e o IoT da filial, o túnel pode subir, mas nenhum tráfego passa por ele. As políticas da primeira versão (com o nome `VPN-MTZ-FIL`) precisam ser reaplicadas com o nome novo.
- Para as máquinas das VLANs 5 e 8 entrarem no domínio, o firewall precisa liberar até 10.0.3.10 as portas do AD (DNS, Kerberos, LDAP, SMB, RPC e tempo).

### Conectividade com a internet

Verificação pelo CLI registrada em 21/10/2026, com a WAN em 172.16.33.35/24:

| Teste | Resultado |
|-------|-----------|
| Ping para o gateway da WAN | Sucesso |
| Ping para 8.8.8.8 | Sucesso |
| Ping para google.com | Falha na resolução de nome |

A conectividade IP funcionava; a falha de resolução indica problema de DNS ainda não confirmado, pois a configuração não pôde ser lida pelo CLI. A rota padrão registrada então usava o gateway 172.16.33.254, fora da rede 172.16.31.0/24 atual, o que sugere que o endereço mudou. A conferência deve ser refeita com `get router info routing-table all` e `execute ping 8.8.8.8`.

## Firewall do meio

| Item | Valor |
|------|-------|
| Modelo | FortiGate-VM (sem VDOM) |
| IP de gerência (projeto) | 192.168.100.1 |
| `port1` (WAN) | 172.16.31.43/24 (DHCP), up |
| `port2` | Sem IP, up |
| Criptografia disponível | Apenas DES e NULL ([diferenças em relação à produção](07-vpn.md#diferenças-entre-laboratório-e-produção)) |

O meio decide quem passa para onde. Políticas provisórias entre servidores e IoT (`MTZ-para-FIL-IoT` e `FIL-IoT-para-MTZ`) usam a `port1` como saída até o túnel da filial existir; depois a interface passa a ser `VPN-MEIO-FIL` ([configs/fortigate-meio.txt](../configs/fortigate-meio.txt)).

## Firewall da filial

| Item | Valor |
|------|-------|
| Modelo | FortiGate (a confirmar) |
| VDOM | `FGT-VDOM-FILIAL-SENAI` (VDOM NEOXGATE) |
| IP de gerência (projeto) | 192.168.100.20 |
| `lan1` | 192.168.200.1/24, secundário 10.1.5.1/24, up |
| `lan2` (WAN) | DHCP, 0.0.0.0, down |
| `a` | 172.16.23.1/24, down |

Funções previstas: controle de acesso à internet e à nuvem, navegação encaminhada pela matriz (atravessa o meio e sai pela `lan2` da matriz com NAT, em etapa posterior), bloqueio entre PCs da filial e da matriz e terminação das VPNs.
