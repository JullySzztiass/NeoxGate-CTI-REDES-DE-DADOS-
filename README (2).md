<h1>Projeto Rede de Dados – Tutoria 2026</h1> 

<p align="center">
  <img src="https://img.shields.io/static/v1?label=Firewall&message=seguranca&color=red&style=for-the-badge"/>
  <img src="https://img.shields.io/static/v1?label=VPN&message=anel%20redundante&color=blue&style=for-the-badge"/>
  <img src="https://img.shields.io/static/v1?label=Cloud&message=AWS%20%2F%20Azure&color=orange&style=for-the-badge&logo=amazonaws"/>
  <img src="https://img.shields.io/static/v1?label=IoT&message=PIR%20%7C%20LDR%20%7C%20Ultrassonico&color=green&style=for-the-badge"/>
  <img src="http://img.shields.io/static/v1?label=License&message=MIT&color=green&style=for-the-badge"/>
  <img src="http://img.shields.io/static/v1?label=STATUS&message=EM%20DESENVOLVIMENTO&color=RED&style=for-the-badge"/>
</p>

> Status do Projeto: :warning: (em desenvolvimento)

### Tópicos 

:small_blue_diamond: [Descrição do projeto](#descrição-do-projeto)

:small_blue_diamond: [Funcionalidades](#funcionalidades)

:small_blue_diamond: [Layout ou Deploy da Aplicação](#layout-ou-deploy-da-aplicação-dash)

:small_blue_diamond: [Configuração dos Firewalls](#configuração-dos-firewalls-fire)

:small_blue_diamond: [Segmentação de Rede (VLANs)](#segmentação-de-rede-vlans-floppy_disk)

:small_blue_diamond: [VPNs e Anel de Redundância](#vpns-e-anel-de-redundância-link)

:small_blue_diamond: [Sensores e Alarme](#sensores-e-alarme-rotating_light)

:small_blue_diamond: [Aplicação e Banco de Dados](#aplicação-e-banco-de-dados-computer)

:small_blue_diamond: [Pré-requisitos](#pré-requisitos)

:small_blue_diamond: [Como rodar a aplicação](#como-rodar-a-aplicação-arrow_forward)

:small_blue_diamond: [Como rodar os testes](#como-rodar-os-testes)

:small_blue_diamond: [Casos de Uso](#casos-de-uso)

:small_blue_diamond: [Iniciando/Configurando banco de dados](#iniciandoconfigurando-banco-de-dados)

:small_blue_diamond: [Linguagens, dependencias e libs utilizadas](#linguagens-dependencias-e-libs-utilizadas-books)

:small_blue_diamond: [Tarefas em aberto](#tarefas-em-aberto)

:small_blue_diamond: [Desenvolvedores/Contribuintes](#desenvolvedorescontribuintes-octocat)

## Descrição do projeto 

<p align="justify">
  Projeto desenvolvido na Tutoria 2026, em parceria entre SENAI e CTI, que simula um ambiente corporativo completo com matriz, filial e nuvem. O objetivo é aplicar conceitos de roteamento, segurança[...]
</p>

<p align="justify">
  Além dos aspectos técnicos, o projeto busca desenvolver soft skills, como o trabalho em equipe multicultural e multidisciplinar.
</p>

## Funcionalidades

**Matriz**

:heavy_check_mark: Firewall com controle de acesso à internet (navegação) e à nuvem  

:heavy_check_mark: Acesso à rede restrito a notebooks e desktops autorizados pela empresa  

:heavy_check_mark: Acesso remoto a servidor Windows com autenticação SSO, restrito a usuários com permissão (dupla autenticação é diferencial)  

:heavy_check_mark: VPN entre o firewall da matriz e a nuvem (banco de dados)  

:heavy_check_mark: VPN entre o firewall da matriz e o da filial  

:heavy_check_mark: Isolamento entre os PCs da matriz e da filial  

**Filial (escritório de vendas, 5 pessoas)**

:heavy_check_mark: Firewall com controle de acesso à internet e à nuvem  

:heavy_check_mark: Acesso à rede restrito a equipamentos autorizados  

:heavy_check_mark: VPN para a nuvem e VPN para a matriz  

:heavy_check_mark: Navegação na internet realizada através do firewall da matriz  

**Ambos os escritórios (segurança física / IoT)**

:heavy_check_mark: Sensores de movimento (PIR), luminosidade (LDR) e presença/distância (ultrassônico)  

:heavy_check_mark: Alarme acionado na matriz e na filial em caso de evento fora do padrão  

:heavy_check_mark: Sensores em rede separada (VLAN10), acessível pelos dois escritórios  

:heavy_check_mark: Monitoramento dos sensores e gerenciamento de logs  

**Rede VPN em anel**

:heavy_check_mark: Anel interligando matriz, filial e nuvem (AWS/Azure)  

:heavy_check_mark: Em caso de falha de um link, a comunicação segue pelo caminho remanescente  

## Layout ou Deploy da Aplicação :dash:

> Inserir o diagrama da topologia (matriz, filial, nuvem, VPNs e VLANs).

![Topologia da rede](caminho/para/topologia.png)

### Componentes da topologia

| Local | Componentes | Detalhes |
| -------- | -------- | -------- |
| Matriz | Firewall, switch(es), servidor de aplicação, servidor Windows, PCs de colaboradores e TI, sensores | A definir |
| Filial | Firewall, switch, PCs de vendas (5 usuários), sensores | A definir |
| Nuvem | Banco de dados (AWS/Azure) | A definir |

## Configuração dos Firewalls :fire:

### Firewall da Matriz

- Controle de acesso à internet (navegação) e à nuvem
- Bloqueio de tráfego entre os PCs da matriz e os da filial
- Liberação das VLANs conforme a tabela de [VLANs](#segmentação-de-rede-vlans-floppy_disk)
- Terminação das VPNs (nuvem e filial)
- Saída de internet da filial

| Item | Valor |
| -------- | -------- |
| Marca/Modelo | A definir |
| IP de gerência | A definir |
| Regras implementadas | A definir |

### Firewall da Filial

- Controle de acesso à internet e à nuvem
- Navegação encaminhada através do firewall da matriz
- Bloqueio de tráfego entre os PCs da filial e os da matriz
- Terminação das VPNs (nuvem e matriz)

| Item | Valor |
| -------- | -------- |
| Marca/Modelo | A definir |
| IP de gerência | A definir |
| Regras implementadas | A definir |

## Segmentação de Rede (VLANs) :floppy_disk:

|VLAN|Finalidade|Quem acessa / Regras de acesso|Faixa de IP|
| -------- |-------- |-------- |-------- |
|VLAN3|Servidores|Hospeda apenas os servidores e suas aplicações|A definir|
|VLAN5|Colaboradores|Acessa as aplicações dos servidores (VLAN3)|A definir|
|VLAN8|TI|Acessa os servidores por portas pré-definidas (VLAN3) e os equipamentos dos colaboradores (VLAN5)|A definir|
|VLAN10|Sensores IoT|Rede apartada da rede local, acessível por matriz e filial|A definir|

## VPNs e Anel de Redundância :link:

| Túnel | Origem | Destino | Finalidade | Tipo/Protocolo |
| -------- | -------- | -------- | -------- | -------- |
| VPN 1 | Matriz | Nuvem | Acesso ao banco de dados | A definir |
| VPN 2 | Matriz | Filial | Comunicação entre escritórios | A definir |
| VPN 3 | Filial | Nuvem | Acesso ao banco de dados | A definir |

O anel interliga matriz, filial e nuvem. Se um dos links falhar, a comunicação de todos os pontos continua pelo caminho remanescente.

| Item | Valor |
| -------- | -------- |
| Protocolo de roteamento/failover | A definir |
| Tempo de convergência | A definir |

## Sensores e Alarme :rotating_light:

| Sensor | Função | Onde |
| -------- | -------- | -------- |
| PIR | Detecção de movimento | Matriz e filial |
| LDR | Detecção de luminosidade | Matriz e filial |
| Ultrassônico | Detecção de distância/presença | Matriz e filial |

- Os sensores trafegam na **VLAN10**, separada da rede local e acessível pelos dois escritórios
- O alarme é acionado na matriz e na filial quando um sensor detecta algo fora do padrão (ex.: fora do horário de expediente)
- Os sensores são monitorados e os logs são gerenciados

| Item | Valor |
| -------- | -------- |
| Placa/microcontrolador | A definir |
| Protocolo de comunicação | A definir |
| Ferramenta de monitoramento e logs | A definir |

## Aplicação e Banco de Dados :computer:

Aplicação didática de cadastro de clientes, hospedada em servidor na matriz e acessada pelos colaboradores. O banco de dados fica hospedado na nuvem. O foco é a forma, o controle e a segurança do[...]

| Item | Valor |
| -------- | -------- |
| Servidor de aplicação | Matriz (VLAN3) |
| Banco de dados | Nuvem (AWS/Azure) |
| Linguagem/Framework | A definir |
| Banco utilizado | A definir |

## Pré-requisitos

:warning: Firewalls para matriz e filial

:warning: Switches com suporte a VLAN

:warning: Conta em provedor de nuvem (AWS ou Azure)

:warning: Servidor Windows (acesso remoto com SSO)

:warning: Placas/microcontroladores e sensores (PIR, LDR, ultrassônico)

... 

## Como rodar a aplicação :arrow_forward:

Passo a passo para implantar o ambiente:

```
1. Configurar as VLANs (3, 5, 8 e 10) na matriz
2. Configurar os firewalls da matriz e da filial (navegação e controle de acesso)
3. Estabelecer as VPNs em anel (matriz <-> filial <-> nuvem)
4. Provisionar o banco de dados na nuvem
5. Publicar o servidor de aplicação na matriz
6. Configurar acesso remoto com SSO/2FA ao servidor Windows
7. Instalar os sensores e configurar o alarme e a coleta de logs
```

... 

Detalhar os comandos e configurações de cada etapa.

## Como rodar os testes

```
- Derrubar um link do anel e verificar a comunicação
- Testar o bloqueio entre PCs da matriz e da filial
- Testar o acesso entre VLANs conforme a tabela de regras
- Acionar um sensor e validar o alarme na matriz e na filial
```

## Casos de Uso

- Colaborador (VLAN5) acessa a aplicação para cadastrar clientes
- Equipe de TI (VLAN8) acessa os servidores em portas pré-definidas para manutenção
- Usuário remoto acessa o servidor Windows via SSO e dupla autenticação
- Sensor detecta movimento fora do expediente e aciona o alarme nos dois escritórios

## Iniciando/Configurando banco de dados

Inserir os comandos de criação e configuração do banco de dados na nuvem.

## Linguagens, dependencias e libs utilizadas :books:

- Firewall (matriz e filial)
- VPN site-to-site em anel
- AWS / Azure
- Servidor Windows com SSO e 2FA
- Sensores PIR, LDR e ultrassônico
- VLANs

... 

## Resolvendo Problemas :exclamation:

Em [issues]() serão registrados os problemas gerados durante o desenvolvimento e como foram resolvidos. 

## Tarefas em aberto

:memo: Documentar a topologia da rede 

:memo: Documentar as configurações dos firewalls da matriz e da filial 

:memo: Documentar as VLANs 3, 5, 8 e 10 e quem acessa cada uma 

:memo: Documentar as VPNs e o anel (conexão redundante) 

:memo: Documentar a aplicação e o banco de dados na nuvem 

:memo: Documentar os sensores e o alarme 

:memo: Configurar SSO e dupla autenticação 

## Desenvolvedores/Contribuintes :octocat:

| [<img src="https://github.com/JullySzztiass.png" width=115><br><sub>Jully Ferrari</sub>](https://github.com/JullySzztiass) |
| :---: |

## Licença 

The [MIT License]() (MIT)

Copyright :copyright: 2026 - Projeto Rede de Dados (SENAI e CTI)
