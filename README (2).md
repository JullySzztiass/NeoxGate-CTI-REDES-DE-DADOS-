<h1>Projeto Rede de Dados – Tutoria 2026</h1>

<p align="center">
  <img src="https://img.shields.io/static/v1?label=Firewall&message=seguranca&color=red&style=for-the-badge"/>
  <img src="https://img.shields.io/static/v1?label=VPN&message=anel%20redundante&color=blue&style=for-the-badge"/>
  <img src="https://img.shields.io/static/v1?label=Cloud&message=AWS%20%2F%20Azure&color=orange&style=for-the-badge&logo=amazonaws"/>
  <img src="https://img.shields.io/static/v1?label=IoT&message=PIR%20%7C%20LDR%20%7C%20Ultrassonico&color=green&style=for-the-badge"/>
  <img src="http://img.shields.io/static/v1?label=License&message=MIT&color=green&style=for-the-badge"/>
  <img src="http://img.shields.io/static/v1?label=STATUS&message=EM%20DESENVOLVIMENTO&color=RED&style=for-the-badge"/>
</p>

> Status do projeto: :warning: em desenvolvimento

---

<details>
<summary><strong>📋 Sumário (clique para expandir/retrair)</strong></summary>

- [Descrição do projeto](#descrição-do-projeto)
- [Objetivo da infraestrutura](#objetivo-da-infraestrutura)
- [Funcionalidades](#funcionalidades)
- [Arquitetura da solução](#arquitetura-da-solução)
- [Endereçamento da rede](#endereçamento-da-rede)
- [Segmentação de rede (VLANs)](#segmentação-de-rede-vlans)
- [Configuração dos firewalls](#configuração-dos-firewalls)
- [VPNs e redundância em anel](#vpns-e-redundância-em-anel)
- [Sensores e alarme](#sensores-e-alarme)
- [Aplicação e banco de dados](#aplicação-e-banco-de-dados)
- [Pré-requisitos](#pré-requisitos)
- [Como rodar a aplicação](#como-rodar-a-aplicação)
- [Como rodar os testes](#como-rodar-os-testes)
- [Casos de uso](#casos-de-uso)
- [Banco de dados](#banco-de-dados)
- [Tarefas em aberto](#tarefas-em-aberto)
- [Desenvolvedores](#desenvolvedorescontribuintes-octocat)
- [Licença](#licença)

</details>

---

## Descrição do projeto

<p align="justify">
  Projeto desenvolvido na Tutoria 2026, em parceria entre SENAI e CTI, com foco em simular um ambiente corporativo completo composto por matriz, filial e nuvem. A solução foi pensada para aplicar conceitos de roteamento, segurança, segmentação de rede, VPN, redundância e monitoramento de sensores IoT em uma infraestrutura didática e funcional.
</p>

<p align="justify">
  O cenário representa uma empresa com múltiplos locais, exigindo regras de acesso, isolamento de tráfego, comunicação segura entre pontos e monitoramento de ambientes físicos. Além dos desafios técnicos, o projeto também estimula trabalho em equipe, documentação e planejamento de infraestrutura.
</p>

---

## Objetivo da infraestrutura

O projeto tem como objetivo demonstrar como uma organização pode:

- segmentar a rede em VLANs por função
- controlar acesso por firewalls
- conectar escritórios por VPNs seguras
- manter redundância para continuidade do serviço
- proteger o ambiente de sensores e dispositivos IoT
- integrar aplicações corporativas com banco de dados em nuvem

---

## Funcionalidades

### Matriz

- :heavy_check_mark: Firewall com controle de acesso à internet e à nuvem
- :heavy_check_mark: Rede restrita a computadores autorizados
- :heavy_check_mark: Acesso remoto ao servidor Windows com autenticação SSO
- :heavy_check_mark: VPN entre matriz e nuvem
- :heavy_check_mark: VPN entre matriz e filial
- :heavy_check_mark: Isolamento entre PCs da matriz e da filial

### Filial

- :heavy_check_mark: Firewall com controle de acesso à internet e à nuvem
- :heavy_check_mark: Rede restrita a equipamentos autorizados
- :heavy_check_mark: VPN para matriz e nuvem
- :heavy_check_mark: Navegação pela internet com saída por meio da matriz

### Segurança física / IoT

- :heavy_check_mark: Sensores PIR, LDR e ultrassônico
- :heavy_check_mark: Alarme acionado em caso de evento fora do padrão
- :heavy_check_mark: Sensores em rede separada (VLAN10)
- :heavy_check_mark: Monitoramento e logs dos eventos

### Rede VPN em anel

- :heavy_check_mark: Conexão entre matriz, filial e nuvem
- :heavy_check_mark: Redundância para manter comunicação em caso de falha de um link

---

## Arquitetura da solução

> Estrutura geral do ambiente de redes corporativas e IoT.

### Diagrama da Matriz

```
                 +---------------------------+
                 |          NUVEM            |
                 |  Banco de Dados          |
                 |  VPC / Banco: 10.1.1.0/24 |
                 +------------+--------------+
                              |
                              | VPN /30 172.31.0.4/30
                              |
    ┌────────────────────────────────────────────────────┐
    │                                                     │
    │  FIREWALL 1 (Matriz) - 192.168.100.10              │
    │         ↕ VPN (172.31.0.0/30)                     │
    │  FIREWALL 2 (Filial) - 192.168.100.20              │
    │                                                     │
    │  ┌─────────────────────────────────────────────┐   │
    │  │          SWITCH MATRIZ                      │   │
    │  │  Distribuição Central das VLANs             │   │
    │  └──┬──────────┬──────────┬──────────┬─────────┘   │
    │     │          │          │          │              │
    │     │          │          │          │              │
    │  ┌──▼──┐   ┌──▼──┐   ┌──▼──┐   ┌──▼──┐             │
    │  │VLAN3│   │VLAN5│   │VLAN8│   │VLAN10            │
    │  │ SRV │   │ COL │   │ TI  │   │ IoT  │            │
    │  └─────┘   └─────┘   └─────┘   └─────┘            │
    │                                                     │
    │  Servidores: Apache, Zabbix, MQTT, AD DS          │
    │  Sensores: PIR, LDR, Ultrassônico (ESP32)         │
    │                                                     │
    └────────────────────────────────────────────────────┘
```

### Componentes da topologia

| Local | Componentes | Detalhes |
| --- | --- | --- |
| Matriz | Firewall, switch(es), servidor de aplicação, servidor Windows, PCs de colaboradores e TI, sensores | Rede principal da empresa e acesso à nuvem |
| Filial | Firewall, switch, PCs de vendas (5 usuários), sensores | Escritório local com comunicação segura com matriz |
| Nuvem | Banco de dados em AWS/Azure | Infraestrutura centralizada para dados e serviços |

---

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

---

## Segmentação de rede (VLANs)

| VLAN | Finalidade | Quem acessa / regras | Faixa de IP |
| --- | --- | --- | --- |
| VLAN3 | Servidores | Hospeda servidores e aplicações | 10.0.3.0/24 |
| VLAN5 | Colaboradores | Acessa aplicações dos servidores | 10.0.5.0/24 |
| VLAN8 | TI | Acessa servidores e equipamentos de colaboradores por portas pré-definidas | 10.0.8.0/24 |
| VLAN10 | Sensores IoT | Rede isolada, acessível por matriz e filial | 10.0.10.0/24 |

---

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

---

## VPNs e redundância em anel

| Túnel | Origem | Destino | Finalidade | Tipo / Protocolo |
| --- | --- | --- | --- | --- |
| VPN 1 | Matriz | Nuvem | Acesso ao banco de dados | IPsec / site-to-site |
| VPN 2 | Matriz | Filial | Comunicação entre escritórios | IPsec / site-to-site |
| VPN 3 | Filial | Nuvem | Acesso ao banco de dados | IPsec / site-to-site |

### Túneis VPN

- M–F: 172.31.0.0/30
- M–N: 172.31.0.4/30
- F–N: 172.31.0.8/30

O anel interliga matriz, filial e nuvem. Caso um link falhe, a comunicação continua pelo caminho restante.

| Item | Valor |
| --- | --- |
| Protocolo de roteamento / failover | OSPF / roteamento dinâmico com redundância em anel |
| Tempo de convergência | A definir |

---

## Sensores e alarme

| Sensor | Função | Onde |
| --- | --- | --- |
| PIR | Detecção de movimento | Matriz e filial |
| LDR | Detecção de luminosidade | Matriz e filial |
| Ultrassônico | Detecção de distância / presença | Matriz e filial |

- Os sensores trafegam na VLAN10, separada da rede local
- O alarme é acionado na matriz e na filial quando um sensor detecta evento fora do padrão
- Os sensores são monitorados e os logs são gerenciados

| Item | Valor |
| --- | --- |
| Placa / microcontrolador | A definir |
| Protocolo de comunicação | A definir |
| Ferramenta de monitoramento e logs | A definir |

---

## Aplicação e banco de dados

Aplicação didática de cadastro de clientes, hospedada em servidor na matriz e acessada pelos colaboradores. O banco de dados fica na nuvem, com foco em segurança, controle de acesso e infraestrutura de rede.

| Item | Valor |
| --- | --- |
| Servidor de aplicação | Matriz (VLAN3) |
| Banco de dados | Nuvem (AWS/Azure) |
| Linguagem / Framework | A definir |
| Banco utilizado | A definir |
| Rede do banco | 10.1.1.0/24 |

---

## Pré-requisitos

- :warning: Firewalls para matriz e filial
- :warning: Switches com suporte a VLAN
- :warning: Conta em provedor de nuvem (AWS ou Azure)
- :warning: Servidor Windows com acesso remoto e SSO
- :warning: Sensores PIR, LDR e ultrassônico
- :warning: Equipamentos de monitoramento e registros de eventos

---

## Como rodar a aplicação

1. Configurar as VLANs (3, 5, 8 e 10) na matriz
2. Configurar os firewalls da matriz e da filial
3. Estabelecer as VPNs em anel (matriz <-> filial <-> nuvem)
4. Provisionar o banco de dados na nuvem
5. Publicar o servidor de aplicação na matriz
6. Configurar o acesso remoto com SSO e 2FA ao servidor Windows
7. Instalar os sensores e configurar o alarme e a coleta de logs

---

## Como rodar os testes

```bash
- Derrubar um link do anel e verificar a comunicação
- Testar o bloqueio entre PCs da matriz e da filial
- Validar o acesso entre VLANs conforme a tabela de regras
- Acionar um sensor e validar o alarme na matriz e na filial
```

---

## Casos de uso

- Colaborador da VLAN5 acessa a aplicação para cadastrar clientes
- Equipe de TI da VLAN8 acessa servidores em portas pré-definidas
- Usuário remoto acessa o servidor Windows com SSO e dupla autenticação
- Sensor detecta movimento fora do expediente e dispara alarme nos dois escritórios

---

## Banco de dados

Inserir os comandos de criação e configuração do banco de dados na nuvem.

Exemplo ilustrativo:

```bash
CREATE DATABASE neoxgate;
CREATE USER app_user WITH PASSWORD 'senha123';
GRANT ALL PRIVILEGES ON DATABASE neoxgate TO app_user;
```

---

## Linguagens, dependências e libs utilizadas

- Firewall (matriz e filial)
- VPN site-to-site em anel
- AWS / Azure
- Servidor Windows com SSO e 2FA
- Sensores PIR, LDR e ultrassônico
- VLANs
- Banco de dados em nuvem
- Redes corporativas e ACLs

---

## Tarefas em aberto

- :memo: Documentar a topologia da rede
- :memo: Documentar as configurações dos firewalls da matriz e da filial
- :memo: Documentar as VLANs 3, 5, 8 e 10 e quem acessa cada uma
- :memo: Documentar as VPNs e o anel de redundância
- :memo: Documentar a aplicação e o banco de dados na nuvem
- :memo: Documentar os sensores e o alarme
- :memo: Configurar SSO e dupla autenticação

---

## Desenvolvedores/Contribuintes :octocat:

| [<img src="https://github.com/JullySzztiass.png" width=115><br><sub>Jully Ferrari</sub>](https://github.com/JullySzztiass) |
| :---: |

---

## Licença

The [MIT License]() (MIT)

Copyright :copyright: 2026 - Projeto Rede de Dados (SENAI e CTI)
