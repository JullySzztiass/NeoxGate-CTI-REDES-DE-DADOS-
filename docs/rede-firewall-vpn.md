# Rede, firewall e VPN

Endereços e faixas dos túneis em [arquitetura.md](arquitetura.md). Configurações completas em [`configs/`](../configs/).

## Switch da matriz

`SW-MATRIZ` (Cisco WS-C2960S-24TD-L), com as VLANs 3, 5, 8 e 10 criadas.

| Porta | Função | Configuração |
|-------|--------|--------------|
| Gi4/0/1 | Uplink do firewall da matriz | Trunk, VLANs 3, 5, 8 e 10 |
| Gi4/0/2 | Servidor ESXi | Trunk, VLAN 3 nativa, VLANs 3 e 10 permitidas |
| Gi4/0/3 | Colaboradores | Acesso, VLAN 5 |
| Gi4/0/4 | TI | Acesso, VLAN 8 |
| Gi4/0/5 | IoT físico | Acesso, VLAN 10 |

## Políticas do firewall da matriz

FortiGate `FGT-VDOM-MTZ-SENAI`, conforme `show firewall policy`.

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

**Política 13 (syslog).** O serviço `SYSLOG` padrão cobre só UDP 514, e o `RSH` (TCP 514 com porta de origem 512–1023) não serve para syslog TCP. Por isso foram criados `SYSLOG-UDP-514` e `SYSLOG-TCP-514` e acrescentados à política, que continua restrita ao par de hosts. Verificação: `show firewall policy 13` e `diagnose debug flow` filtrado por origem 10.0.10.10, destino 10.0.3.20 e porta 514, que deve mostrar `Allowed by Policy-13`.

**Pendente para o domínio.** Para máquinas das VLANs 5 e 8 entrarem no domínio, o firewall precisa liberar até 10.0.3.10 as portas do AD (DNS, Kerberos, LDAP, SMB, RPC e tempo).

## VPN

### Desenho

O primeiro túnel direto matriz–filial (`VPN-MTZ-FIL`) não seguia o diagrama do projeto, que passa as duas VPNs pelo firewall do meio. O desenho atual tem dois túneis, e o anel fecha em filial → meio → matriz → nuvem → filial:

- `VPN-FIL-MEIO` (filial ↔ meio)
- `VPN-MTZ-MEIO` (meio ↔ matriz), pela WAN `172.16.31.0/24` (`lan2` da matriz, `port1` do meio)

Filial e matriz só se alcançam pelo meio. Objetos, políticas e o isolamento dos PCs foram mantidos: as políticas liberam apenas servidores e IoT entre as pontas, sem nenhuma política entre os PCs da matriz e da filial. A navegação da filial, que o enunciado manda sair pela matriz, atravessará o meio e sairá pela `lan2` da matriz com NAT, em etapa posterior.

### Estado dos firewalls (10/11/2026)

| Firewall | Estado |
|----------|--------|
| Matriz | Túnel `VPN-MTZ-MEIO` configurado; fica em `connecting` |
| Meio (FortiGate-VM, sem VDOM) | Recebe o IKE_SA_INIT e descarta com `no policy configured`; túnel e rotas aplicados, resultado não confirmado |
| Filial | `lan1` em operação; `lan2` (WAN) sem link nem IP; túnel ainda não montado |
| Nuvem (AWS) | Faixas definidas, sem configuração registrada |

Falta aplicar na matriz as políticas entre servidores/IoT e o IoT da filial com a interface `VPN-MTZ-MEIO` (nenhuma política a referencia hoje). Sem elas o túnel pode subir, mas nenhum tráfego passa.

### Diagnóstico do túnel matriz-meio

A rede entre os dois firewalls está ok (ping nos dois sentidos) e o sniffer mostra o IKE chegando ao meio. O `diagnose debug application ike -1` indica `ignoring IKEv2 request, no policy configured`: o meio rejeita o pedido antes de comparar algoritmo ou senha. Rede, proposal, fase 1 e fase 2 foram descartados como causa.

**Hipótese principal (não confirmada):** em FortiGates recentes, o IKE de um túnel de interface parece ignorar o pedido quando nenhuma política de firewall referencia a interface. Teste:

1. Aplicar no meio a interface do túnel e as políticas (ver [`configs/fortigate-meio.txt`](../configs/fortigate-meio.txt)).
2. Na matriz, executar `diagnose vpn ike gateway clear name VPN-MTZ-MEIO` e depois `diagnose vpn ike gateway list`.
3. Esperado: `status: established`, com ping entre 172.31.0.13 e 172.31.0.14.

Se continuar igual: conferir `get system status` do meio (`License Status` e `VM Resource`), testar `set pfs disable` no phase2 do meio, ou substituir o meio por um pfSense.

### Diferenças em relação ao ambiente de produção

Pontos para constar no relatório e na apresentação à banca.

| Ponto | Laboratório | Produção |
|-------|-------------|----------|
| Criptografia | `des-sha256`, DH 14: o FortiGate-VM do meio só oferece DES e NULL (causa provável: licença de avaliação, não confirmada) | `aes256-sha256`, DH 14 |
| IPs dos túneis | `remote-gw` fixo apontando para IPs entregues por DHCP (172.16.31.30 e 172.16.31.43); se mudarem, o túnel cai | Reserva de DHCP pela CTI ou IP fixo |
| Rota padrão da matriz | Coletada antes com gateway 172.16.33.254, fora da rede atual: conferir com `get router info routing-table all` | — |

**Persistência no FortiGate.** Cada bloco `set … next … end` já grava na configuração em execução e sobrevive a reinício; não existe "salvar". Fazer backup (Configuration > Backup) antes de mudanças grandes e anexar ao relatório.

## Filial

- **Rede interna.** `lan1` em 192.168.200.1/24 com secundário 10.1.5.1/24, acessível pelo console COM4. O Kali (VirtualBox, 192.168.200.50) não resolve ARP com a `lan1`, o que aponta para cabo, porta ou adaptador da VM, não para regra de firewall. Verificar, nesta ordem: `diagnose sniffer packet lan1 'arp or icmp' 4 10` no firewall; `ip neigh show` no Kali; adaptador em modo bridge na placa Ethernet física, com "Cabo conectado" e modo promíscuo "Permitir tudo"; cabo direto entre notebook e `lan1`.
- **WAN (`lan2`).** Down com IP 0.0.0.0, o que indica falta de link físico; o grupo informou que ela não vai pegar IP por DHCP. Alternativas:

| Opção | Como | Condição |
|-------|------|----------|
| IP fixo na 172.16.31.0/24 | `set mode static` com IP livre | A CTI confirmar um IP livre |
| Liberar o MAC no DHCP | Pedir à CTI para liberar o MAC da `lan2` | Rede do SENAI/CTI filtrar por MAC |
| Cabo direto filial ↔ meio | `port2` do meio 172.16.40.1/30 e `lan2` da filial 172.16.40.2/30 como `remote-gw` | O diagrama do projeto permitir esse cabo |

O modelo de configuração do túnel da filial está em [`configs/fortigate-filial-modelo.txt`](../configs/fortigate-filial-modelo.txt) e pode ser colado pelo console.

## Comandos de verificação

| Objetivo | Comando | Onde |
|----------|---------|------|
| IPs e link das interfaces | `get system interface physical` | Os três |
| Testar a WAN matriz–meio | `execute ping 172.16.31.43` / `execute ping 172.16.31.30` | Matriz / meio |
| Estado do túnel IKE | `diagnose vpn ike gateway list` | Matriz e meio |
| Reiniciar a negociação | `diagnose vpn ike gateway clear name VPN-MTZ-MEIO` | Matriz |
| Ver se o IKE chega | `diagnose sniffer packet port1 'host 172.16.31.30 and udp port 500' 4 20` | Meio |
| Depurar o IKE | `diagnose debug application ike -1` e `diagnose debug enable`; parar com `diagnose debug disable` e `diagnose debug reset` | Meio |
| Conferir fase 1 e fase 2 | `show full-configuration vpn ipsec phase1-interface <nome>` e `…phase2-interface <nome>` | Meio |
| Listar proposals aceitos | `set proposal ?` dentro do `edit` | Meio |
| Políticas e serviços | `show firewall policy`, `show firewall service custom` | Matriz |
| Seguir um fluxo | `diagnose debug flow filter saddr 10.0.10.10`, `daddr 10.0.3.20`, `dport 514`, `diagnose debug flow trace start 20` | Matriz |
| ARP e camada 2 | `get system arp`, `diagnose sniffer packet lan1 'arp or icmp' 4 10` | Filial |
| Licença e versão | `get system status` | Meio |
| Rotas e internet | `get router info routing-table all`, `execute ping 8.8.8.8` | Matriz |

**Evidências já coletadas, úteis como anexo:** `gateway list` da matriz em `connecting`, sniffer do meio mostrando o IKE chegando, debug com `no policy configured`, `show full-configuration` do phase1 e phase2 do meio, `show firewall policy` da matriz e `show firewall service custom`. Senhas pré-compartilhadas ficam fora do relatório.
