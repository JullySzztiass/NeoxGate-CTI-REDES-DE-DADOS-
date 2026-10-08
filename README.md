<h1>Projeto Rede de Dados – Tutoria 2026</h1>

<p align="center">
  <img src="https://img.shields.io/static/v1?label=Firewall&message=seguranca&color=red&style=for-the-badge"/>
  <img src="https://img.shields.io/static/v1?label=VPN&message=anel%20redundante&color=blue&style=for-the-badge"/>
  <img src="https://img.shields.io/static/v1?label=Cloud&message=AWS%20%2F%20Azure&color=orange&style=for-the-badge&logo=amazonaws"/>
  <img src="https://img.shields.io/static/v1?label=IoT&message=Camara%20Emulada&color=green&style=for-the-badge"/>
  <img src="http://img.shields.io/static/v1?label=License&message=MIT&color=green&style=for-the-badge"/>
  <img src="http://img.shields.io/static/v1?label=STATUS&message=EM%20DESENVOLVIMENTO&color=RED&style=for-the-badge"/>
</p>

> Status do projeto: :warning: em desenvolvimento. Cada seção indica o que está **implementado** e o que está **planejado**; o consolidado está em [Pendências](#pendências).

<details open>
<summary><strong>SUMÁRIO</strong></summary>

:small_blue_diamond: [Descrição do projeto](#descrição-do-projeto)

:small_blue_diamond: [Arquitetura da solução](#arquitetura-da-solução)

:small_blue_diamond: [Endereçamento da rede](#endereçamento-da-rede)

:small_blue_diamond: [Switch da matriz](#switch-da-matriz)

:small_blue_diamond: [Firewalls](#firewalls)

:small_blue_diamond: [VPNs e redundância em anel](#vpns-e-redundância-em-anel)

:small_blue_diamond: [IoT - Câmera emulada](#iot---câmera-emulada)

:small_blue_diamond: [Servidor de monitoramento e logs](#servidor-de-monitoramento-e-logs)

:small_blue_diamond: [Pendências](#pendências)

:small_blue_diamond: [Desenvolvedores/Contribuintes](#desenvolvedorescontribuintes-octocat)

:small_blue_diamond: [Licença](#licença)

</details>

## Descrição do projeto

<p align="justify">
  Projeto desenvolvido na Tutoria 2026, em parceria entre SENAI e CTI, que simula um ambiente corporativo composto por matriz, filial e nuvem, aplicando conceitos de redes, segurança e IoT.
</p>

Objetivos:

- segmentar a rede em VLANs por função
- controlar o acesso por firewalls
- conectar os sites por VPNs, com redundância em anel
- isolar dispositivos IoT (câmera emulada)
- integrar aplicações corporativas a um banco de dados em nuvem

## Arquitetura da solução

> Topologia inicial de requisição do projeto.

<p align="center">
  <img src="./imagens/topologia-inicial.png" alt="Topologia inicial CTI" width="100%" />
</p>

> Diagrama da topologia da Matriz.

<p align="center">
  <img src="./imagens/diagrama.jpg" alt="Diagrama da topologia de rede" width="100%" />
</p>

| Local | Componentes | Detalhes |
| --- | --- | --- |
| Matriz | Firewall, switch, servidor de aplicação, servidor Windows, PCs de colaboradores e TI, câmera IoT | Rede principal e acesso à nuvem |
| Filial | Firewall, switch, PCs (3 usuários), câmera IoT | Escritório local, conectado à matriz por VPN |
| Nuvem | Banco de dados (AWS ou Azure) | Dados centralizados |

## Endereçamento da rede

Convenção: `10.0.x.0` para a matriz e `10.1.x.0` para a filial.

### Matriz

| Elemento | Endereço |
| --- | --- |
| VLAN 3 - Servidores | 10.0.3.0/24 |
| VLAN 5 - Colaboradores | 10.0.5.0/24 |
| VLAN 8 - TI | 10.0.8.0/24 |
| VLAN 10 - IoT | 10.0.10.0/24 |
| Gateway das VLANs | `.1` de cada faixa (firewall) |
| Firewall da matriz (gerência) | 192.168.100.10/24 |

### Filial

| Elemento | Endereço |
| --- | --- |
| VLAN 5 - PCs | 10.1.5.0/24 |
| VLAN 10 - IoT | 10.1.10.0/24 |
| Firewall da filial (gerência) | 192.168.100.20/24 |

### Interconexão e nuvem

| Elemento | Endereço |
| --- | --- |
| Firewall do meio | 192.168.100.1/24 |
| Banco de dados / VPC | 10.1.1.0/24 |
| Túnel Matriz–Filial | 172.31.0.0/30 |
| Túnel Matriz–Nuvem | 172.31.0.4/30 |
| Túnel Filial–Nuvem | 172.31.0.8/30 |

## Switch da matriz

**Status: implementado e testado.**

O switch é uma VM Debian 12 (sem interface gráfica, disco reduzido) com Open vSwitch, rodando em VMware Workstation. Atua apenas na **camada 2**: segmenta por VLAN e delega o roteamento entre elas ao firewall.

Cada **LAN Segment** do VMware representa um cabo de rede. A VM tem seis placas, e a correspondência entre interface e segmento foi conferida pelo endereço MAC.

| Interface | Segmento | VLAN | Função |
| --- | --- | --- | --- |
| ens33 | NAT | - | Gerência e instalação de pacotes (temporária) |
| ens37 | link-fw | trunk | Uplink para o firewall: todas as VLANs com tag 802.1Q |
| ens38 | vlan3-srv | 3 | Servidores |
| ens39 | vlan5-colab | 5 | Colaboradores |
| ens40 | vlan8-ti | 8 | TI |
| ens41 | vlan10-iot | 10 | IoT / câmera emulada |

### Configuração

```bash
su -
apt update && apt install -y openvswitch-switch
ovs-vsctl add-br br0

ovs-vsctl add-port br0 ens37              # uplink: trunk
ovs-vsctl add-port br0 ens38 tag=3
ovs-vsctl add-port br0 ens39 tag=5
ovs-vsctl add-port br0 ens40 tag=8
ovs-vsctl add-port br0 ens41 tag=10

# Sobe as placas agora e gera o arquivo que as sobe a cada boot
for i in ens37 ens38 ens39 ens40 ens41; do
    ip link set $i up
    printf 'auto %s\niface %s inet manual\n    up ip link set $IFACE up\n\n' "$i" "$i"
done > /etc/network/interfaces.d/ovs-ports
```

A configuração do OVS persiste no próprio banco do serviço (ovsdb). O arquivo `ovs-ports` serve apenas para as interfaces subirem na inicialização.

### Verificação

```bash
ovs-vsctl show             # bridge, portas e tags
ovs-appctl fdb/show br0    # MACs aprendidos por VLAN
```

Saída esperada de `ovs-vsctl show` (resumida):

```
Bridge br0
    Port ens37
        Interface ens37
    Port ens38
        tag: 3
        Interface ens38
    Port ens39
        tag: 5
    ...
```

### Testes de isolamento

Duas VMs de teste foram criadas por clones linkados, para economizar disco.

| Teste | Procedimento | Resultado |
| --- | --- | --- |
| VLANs diferentes | Mesma faixa de IP, em portas de VLANs distintas | Sem comunicação: isolamento confirmado |
| Mesma VLAN | Tag de uma porta alterada com `ovs-vsctl set port <porta> tag=<vlan>` | Comunicação normal; tag original restaurada |

O teste do uplink com o firewall depende do firewall e está em [Pendências](#pendências).

### Roteamento provisório

Enquanto o firewall não existe, o próprio switch roteia entre as VLANs 3 e 10 por duas portas internas, usando os gateways que o firewall assumirá:

```bash
ovs-vsctl --may-exist add-port br0 gw3 tag=3 -- set interface gw3 type=internal
ovs-vsctl --may-exist add-port br0 gw10 tag=10 -- set interface gw10 type=internal
ip addr add 10.0.3.1/24 dev gw3
ip addr add 10.0.10.1/24 dev gw10
ip link set gw3 up
ip link set gw10 up
echo 1 > /proc/sys/net/ipv4/ip_forward
```

- Não persiste após reinício do switch; o bloco deve ser executado novamente.
- Não há filtragem: **as VLANs 3 e 10 se comunicam livremente**. O isolamento entre elas só volta com as regras do firewall.
- Quando o firewall assumir `10.0.3.1` e `10.0.10.1`:

```bash
ovs-vsctl del-port br0 gw3
ovs-vsctl del-port br0 gw10
echo 0 > /proc/sys/net/ipv4/ip_forward
```

Rotas de apoio: no servidor, `ip route add 10.0.10.0/24 via 10.0.3.1`; na câmera, rota para `10.0.3.0/24` via `10.0.10.1`.

## Firewalls

**Status: não iniciado.** Modelo e marca ainda não definidos.

| Firewall | Função prevista |
| --- | --- |
| Matriz | Subinterfaces 802.1Q por VLAN (3, 5, 8, 10) no uplink do switch; controle de acesso à internet e à nuvem; terminação das VPNs com a nuvem e a filial; saída de internet da filial |
| Filial | Controle de acesso à internet e à nuvem; navegação encaminhada pela matriz; terminação das VPNs com a nuvem e a matriz |

Regras previstas:

| Origem | Destino | Ação |
| --- | --- | --- |
| VLAN 10 (IoT) | 10.0.3.20 (servidor de logs), 514/UDP | Permitir |
| VLAN 10 (IoT) | Demais destinos | Negar |
| PCs da matriz | PCs da filial (e vice-versa) | Negar |

## VPNs e redundância em anel

**Status: planejado.**

| Túnel | Origem | Destino | Finalidade | Rede do túnel |
| --- | --- | --- | --- | --- |
| VPN 1 | Matriz | Nuvem | Acesso ao banco de dados | 172.31.0.4/30 |
| VPN 2 | Matriz | Filial | Comunicação entre escritórios | 172.31.0.0/30 |
| VPN 3 | Filial | Nuvem | Acesso ao banco de dados | 172.31.0.8/30 |

Os três túneis (IPsec site-to-site) formam um anel entre matriz, filial e nuvem: se um link falhar, o tráfego segue pelo caminho restante. O protocolo de roteamento e o método de túnel dependem do provedor de nuvem escolhido e estão em [Pendências](#pendências).

## IoT - Câmera emulada

**Status: placeholder implementado; streaming real pendente.**

A câmera é emulada por uma VM Debian 12 (CLI) na VLAN 10, isolada da rede de usuários.

| Item | Valor |
| --- | --- |
| IP na matriz | 10.0.10.10/24 |
| IP na filial | A definir (faixa 10.1.10.0/24) |
| Gateway | 10.0.10.1 |
| Serviço atual | `python3 -m http.server 8080` (placeholder) |
| Protocolo de streaming | A definir (RTSP, HTTP ou MJPEG) |

O servidor Python representa o serviço da câmera. A validação foi feita com `ping` e `curl http://10.0.10.10:8080` a partir de outra máquina da VLAN 10. A instalação do `curl` exigiu ligar uma placa NAT temporária, depois desligada.

Câmeras da matriz e da filial devem se comunicar pela VPN, e os logs devem ir ao servidor central (ver abaixo).

## Servidor de monitoramento e logs

**SRV-LINUX** (Debian 12), `10.0.3.20/24`, VLAN 3.

| Serviço | Status |
| --- | --- |
| Zabbix 7.0 (server, frontend, MariaDB, Apache) e Zabbix Agent 2 | Instalado; interface em `http://10.0.3.20/zabbix` |
| Syslog central (rsyslog, 514/UDP e TCP) | Pendente |
| Monitoramento da câmera (ping e porta 8080) e envio de logs | Pendente |

## Pendências

**Switch**
- Limitar o trunk do uplink: `ovs-vsctl set port ens37 trunks=3,5,8,10`.
- Testar o uplink com o firewall.
- Desativar a placa NAT (`ens33`) antes da entrega.

**Firewalls**
- Definir marca/modelo, VDOMs e subinterfaces; implementar as regras previstas.
- Confirmar a máscara (/24) e a interface dos IPs de gerência.
- Definir o papel do firewall do meio (interconexão/internet simulada).
- Retirar a Bridge temporária do SRV-LINUX e o roteamento provisório do switch.

**VPN e nuvem**
- Definir o provedor (AWS ou Azure) e o banco.
- Definir roteamento do anel: VPNs gerenciadas de nuvem usam BGP; OSPF exigiria túneis baseados em rota (VTI/GRE). Alternativa: rotas estáticas com failover.
- Definir o que a VPN 2 permite, já que PCs de matriz e filial são isolados entre si.
- Listar o IP de cada ponta dos túneis.
- Avaliar mover a VPC para fora do bloco da filial (`10.1.x.x`), por exemplo `10.2.1.0/24`.
- Medir o tempo de convergência.
- Mover a aplicação para o banco na nuvem.

**IoT (câmera emulada)**
- Definir o protocolo de streaming e configurar `ffmpeg`/`motion`.
- Criar a câmera da filial e definir seu IP.
- Implementar syslog central e envio de logs da câmera; monitorá-la no Zabbix.

**Demais componentes**
- Servidor Windows (AD), acesso remoto com SSO e dupla autenticação, controle de dispositivos autorizados.
- Servidor de aplicação e PCs.
- Filial completa e navegação da filial pelo firewall da matriz.
- Monitorar switch e firewall por SNMP no Zabbix.

**Segurança (antes da entrega)**
- Trocar as senhas de teste (MariaDB, `Admin` do Zabbix).
- Restringir a interface do Zabbix à VLAN 8 e ao próprio servidor, após o firewall existir.
- Confirmar o uso de HTTP ou HTTPS no Zabbix.
- A aplicação não tem login: os logs registram só o IP de origem. Avaliar o impacto na LGPD.

## Desenvolvedores/Contribuintes :octocat:

| [<img src="https://github.com/JullySzztiass.png" width=115><br><sub>Jully Ferrari</sub>](https://github.com/JullySzztiass) | [<img src="https://github.com/Dedenyee.png" width=115><br><sub>Vinícius</sub>](https://github.com/Dedenyee) | [<img src="https://github.com/isabellyyvitoria.png" width=115><br><sub>Isabelly</sub>](https://github.com/isabellyyvitoria) | [<img src="https://github.com/gluane.png" width=115><br><sub>Luane</sub>](https://github.com/gluane) | [<img src="https://github.com/kaua-brito.png" width=115><br><sub>Kaua Brito</sub>](https://github.com/kaua-brito) |
| :---: | :---: | :---: | :---: | :---: |
| Documentação | Servidor | Firewall | Firewall | Switch - IoT |

---

## Licença

[MIT License](./LICENSE)

Copyright :copyright: 2026 - Projeto Rede de Dados (SENAI e CTI)

> Referência: [Preparação pré-projeto](https://miro.com/app/board/uXjVHhTdmiQ=/?share_link_id=182961988823)
