<h1>Projeto Rede de Dados – Tutoria 2026</h1>

<p align="center">
  <img src="https://img.shields.io/static/v1?label=Firewall&message=seguranca&color=red&style=for-the-badge"/>
  <img src="https://img.shields.io/static/v1?label=VPN&message=anel%20redundante&color=blue&style=for-the-badge"/>
  <img src="https://img.shields.io/static/v1?label=Cloud&message=AWS%20%2F%20Azure&color=orange&style=for-the-badge&logo=amazonaws"/>
  <img src="https://img.shields.io/static/v1?label=IoT&message=Camara%20Emulada&color=green&style=for-the-badge"/>
  <img src="http://img.shields.io/static/v1?label=License&message=MIT&color=green&style=for-the-badge"/>
  <img src="http://img.shields.io/static/v1?label=STATUS&message=EM%20DESENVOLVIMENTO&color=RED&style=for-the-badge"/>
</p>

> Status do projeto: :warning: em desenvolvimento

<details open>
<summary><strong>SUMÁRIO</strong></summary>

:small_blue_diamond: [Descrição do projeto](#descrição-do-projeto)

:small_blue_diamond: [Arquitetura da solução](#arquitetura-da-solução)

:small_blue_diamond: [Endereçamento da rede](#endereçamento-da-rede)

:small_blue_diamond: [Configuração dos firewalls](#configuração-dos-firewalls)

:small_blue_diamond: [Configuração do switch da matriz](#configuração-do-switch-da-matriz)

:small_blue_diamond: [Testes de funcionamento do switch](#testes-de-funcionamento-do-switch)

:small_blue_diamond: [VPNs e redundância em anel](#vpns-e-redundância-em-anel)

:small_blue_diamond: [IoT - Câmera Emulada](#iot---câmera-emulada)

</details>

## Descrição do projeto

<p align="justify">
  Projeto desenvolvido na Tutoria 2026, em parceria entre SENAI e CTI, com foco em simular um ambiente corporativo completo composto por matriz, filial e nuvem. A solução foi pensada para aplicar conceitos de rede, segurança, segmentação e redundância em um cenário realista de infraestrutura.
</p>

O projeto tem como objetivo demonstrar como uma organização pode:

- segmentar a rede em VLANs por função
- controlar acesso por firewalls
- conectar escritórios por VPNs seguras
- manter redundância para continuidade do serviço
- proteger o ambiente de dispositivos IoT
- integrar aplicações corporativas com banco de dados em nuvem

## Arquitetura da solução

> Topologia inicial de requisição do projeto.

<p align="center">
  <img src="./imagens/Captura de tela 2026-10-07 160145.png" alt="Topologia inicial CTI" width="100%" />
</p> 

> Diagrama do protótipo 1.2 - Matriz.

<p align="center">
  <img src="./imagens/diagrama.jpg" alt="Diagrama da topologia de rede" width="100%" />
</p>

### Componentes da topologia

| Local | Componentes | Detalhes |
| --- | --- | --- |
| Matriz | Firewall, switch, servidor de aplicação, servidor Windows, PCs de colaboradores e TI, câmera IoT | Rede principal da empresa e acesso à nuvem |
| Filial | Firewall, switch, PCs (3 usuários), câmera IoT | Escritório local com comunicação segura com matriz |
| Nuvem | Banco de dados em AWS/Azure | Infraestrutura centralizada para dados e serviços |

## Endereçamento da rede

### Rede matriz

| Elemento | Endereço |
| --- | --- |
| VLAN3 - Servidores | 10.0.3.0/24 |
| VLAN5 - Colaboradores | 10.0.5.0/24 |
| VLAN8 - TI | 10.0.8.0/24 |
| VLAN10 - IoT | 10.0.10.0/24 |
| Firewall da matriz | 192.168.100.10 |

### Rede filial

| Elemento | Endereço |
| --- | --- |
| VLAN10 - IoT | 10.1.10.0/24 |
| PCs | 10.1.5.0/24 |
| Firewall da filial | 192.168.100.20 |

### Interconexão e infraestrutura de suporte

| Elemento | Endereço |
| --- | --- |
| Firewall do meio | 192.168.100.1 |
| Banco de dados / VPC | 10.1.1.0/24 |

## Configuração dos firewalls

### Firewall da matriz

- Controle de acesso à internet e à nuvem
- Bloqueio de tráfego entre PCs da matriz e da filial
- Liberação das VLANs conforme a regra de segmentação
- Terminação das VPNs da nuvem e da filial
- Saída de internet da filial

| Item | Valor |
| --- | --- |
| Marca / Modelo | A definir |
| IP de gerência | 192.168.100.10 |
| Regras implementadas | ACLs para VLAN3, VLAN5, VLAN8, VLAN10, acesso à internet e VPN |

### Firewall da filial

- Controle de acesso à internet e à nuvem
- Navegação encaminhada pela matriz
- Bloqueio de tráfego entre PCs da filial e da matriz
- Terminação das VPNs da nuvem e da matriz

| Item | Valor |
| --- | --- |
| Marca / Modelo | A definir |
| IP de gerência | 192.168.100.20 |
| Regras implementadas | ACLs para VLAN10, PCs, acesso à matriz e à nuvem via VPN |

## Configuração do switch da matriz

### Preparação do ambiente virtual

Foi criada uma VM com Debian 12, sem interface gráfica e com disco reduzido, para exercer a função de switch.

Foram criados cinco LAN Segments, que representam os cabos de rede do ambiente: link-fw, vlan3-srv, vlan5-colab, vlan8-ti e vlan10-iot.

A VM recebeu seis placas de rede, e a correspondência entre os nomes das interfaces (ens33, ens37 a ens41) e os segmentos foi conferida pelos endereços MAC.

### Topologia utilizada

| Interface | Segmento | VLAN | Função |
| --- | --- | --- | --- |
| ens33 | NAT | - | Gerência / acesso externo (instalação de pacotes) |
| ens37 | link-fw | trunk (sem tag) | Uplink com o firewall |
| ens38 | vlan3-srv | 3 | Servidores |
| ens39 | vlan5-colab | 5 | Colaboradores |
| ens40 | vlan8-ti | 8 | TI |
| ens41 | vlan10-iot | 10 | IoT / Câmera emulada |

### Objetivo

O switch atua como ponto de agregação da rede corporativa, separando o tráfego por função e mantendo o ambiente controlado:

- VLAN 3: servidores
- VLAN 5: colaboradores
- VLAN 8: TI
- VLAN 10: dispositivos IoT

### Comandos utilizados

```bash
su -

apt update
apt install openvswitch-switch
ovs-vsctl add-br br0

ovs-vsctl add-port br0 ens37
ovs-vsctl add-port br0 ens38 tag=3
ovs-vsctl add-port br0 ens39 tag=5
ovs-vsctl add-port br0 ens40 tag=8
ovs-vsctl add-port br0 ens41 tag=10

for i in ens37 ens38 ens39 ens40 ens41; do
    ip link set $i up
done

cat > /etc/network/interfaces.d/ovs-ports << 'EOF'
auto ens37
iface ens37 inet manual
    up ip link set $IFACE up

auto ens38
iface ens38 inet manual
    up ip link set $IFACE up

auto ens39
iface ens39 inet manual
    up ip link set $IFACE up

auto ens40
iface ens40 inet manual
    up ip link set $IFACE up

auto ens41
iface ens41 inet manual
    up ip link set $IFACE up
EOF
```

## Desenvolvedores/Contribuintes :octocat:

| [<img src="https://github.com/JullySzztiass.png" width=115><br><sub>Jully Ferrari</sub>](https://github.com/JullySzztiass) | [<img src="https://github.com/Dedenyee.png" width=115><br><sub>Vinícius</sub>](https://github.com/Dedenyee) | [<img src="https://github.com/isabellyyvitoria.png" width=115><br><sub>Isabelly</sub>](https://github.com/isabellyyvitoria) | [<img src="https://github.com/gluane.png" width=115><br><sub>Luane</sub>](https://github.com/gluane) | [<img src="https://github.com/kaua-brito.png" width=115><br><sub>Kaua Brito</sub>](https://github.com/kaua-brito) |
| :---: | :---: | :---: | :---: | :---: |
| Documentação | Servidor | Firewall | Firewall | Firewall |


---

## Licença

The [MIT License]() (MIT)

Copyright :copyright: 2026 - Projeto Rede de Dados (SENAI e CTI)

> Referência: [Preparação pré-projeto](https://miro.com/app/board/uXjVHhTdmiQ=/?share_link_id=182961988823)
