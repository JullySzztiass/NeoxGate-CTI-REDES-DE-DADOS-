Projeto Rede de Dados – Tutoria 2026
<p align="center"> <img src="https://img.shields.io/static/v1?label=Firewall&message=seguranca&color=red&style=for-the-badge"/> <img src="https://img.shields.io/static/v1?label=VPN&message=anel%20redundante&color=blue&style=for-the-badge"/> <img src="https://img.shields.io/static/v1?label=Cloud&message=AWS%20%2F%20Azure&color=orange&style=for-the-badge&logo=amazonaws"/> <img src="https://img.shields.io/static/v1?label=IoT&message=PIR%20%7C%20LDR%20%7C%20Ultrassonico&color=green&style=for-the-badge"/> <img src="http://img.shields.io/static/v1?label=License&message=MIT&color=green&style=for-the-badge"/> <img src="http://img.shields.io/static/v1?label=STATUS&message=EM%20DESENVOLVIMENTO&color=RED&style=for-the-badge"/> </p>

Status do Projeto: :warning: em desenvolvimento

Tópicos

:small_blue_diamond: Descrição do projeto

:small_blue_diamond: Requisitos e funcionalidades

:small_blue_diamond: Topologia e layout da rede

:small_blue_diamond: Segmentação de rede (VLANs)

:small_blue_diamond: Pré-requisitos

:small_blue_diamond: Como implantar o ambiente

:small_blue_diamond: Aplicação de cadastro de clientes

:small_blue_diamond: Tecnologias utilizadas

:small_blue_diamond: Tarefas em aberto

:small_blue_diamond: Desenvolvedores

Descrição do projeto
<p align="justify"> Projeto desenvolvido na Tutoria 2026, em parceria entre SENAI e CTI, que simula um ambiente corporativo completo com matriz, filial e nuvem. O objetivo é aplicar conceitos de roteamento, segurança, resiliência de comunicação, IoT, desenvolvimento e monitoramento, garantindo conectividade, segurança e escalabilidade. </p> <p align="justify"> Além dos aspectos técnicos, o projeto busca desenvolver soft skills, como o trabalho em equipe multicultural e multidisciplinar. </p>
Requisitos e funcionalidades
Matriz

:heavy_check_mark: Firewall com controle de acesso à internet (navegação) e à nuvem

:heavy_check_mark: Acesso à rede restrito a notebooks e desktops autorizados pela empresa

:heavy_check_mark: Acesso remoto a servidor Windows com autenticação SSO, restrito a usuários com permissão (dupla autenticação é diferencial)

:heavy_check_mark: VPN entre o firewall da matriz e a nuvem (banco de dados)

:heavy_check_mark: VPN entre o firewall da matriz e o da filial

:heavy_check_mark: Isolamento entre os PCs da matriz e da filial

Filial (escritório de vendas, 5 pessoas)

:heavy_check_mark: Firewall com controle de acesso à internet e à nuvem

:heavy_check_mark: Acesso à rede restrito a equipamentos autorizados

:heavy_check_mark: VPN para a nuvem e VPN para a matriz

:heavy_check_mark: Navegação na internet realizada através do firewall da matriz

Ambos os escritórios (segurança física / IoT)

:heavy_check_mark: Sensores de movimento (PIR), luminosidade (LDR) e presença/distância (ultrassônico)

:heavy_check_mark: Alarme acionado na matriz e na filial em caso de evento fora do padrão

:heavy_check_mark: Sensores em rede separada (VLAN10), acessível pelos dois escritórios

:heavy_check_mark: Monitoramento dos sensores e gerenciamento de logs

Rede VPN em anel

:heavy_check_mark: Anel interligando matriz, filial e nuvem (AWS/Azure)

:heavy_check_mark: Em caso de falha de um link, a comunicação segue pelo caminho remanescente

Topologia e layout da rede :dash:

Inserir aqui o diagrama da topologia (matriz, filial, nuvem, VPNs e VLANs).

Mostrar Imagem

Segmentação de rede (VLANs)
VLAN	Finalidade	Regras de acesso
VLAN3	Servidores	Hospeda apenas os servidores e suas aplicações
VLAN5	Colaboradores	Acessa as aplicações dos servidores (VLAN3)
VLAN8	TI	Acessa os servidores por portas pré-definidas e os equipamentos dos colaboradores
VLAN10	Sensores IoT	Rede apartada, acessível por matriz e filial
Pré-requisitos

:warning: Firewalls para matriz e filial

:warning: Switches com suporte a VLAN

:warning: Conta em provedor de nuvem (AWS ou Azure)

:warning: Servidor Windows (acesso remoto com SSO)

:warning: Placas/microcontroladores e sensores (PIR, LDR, ultrassônico)

...

Complementar com versões e ferramentas utilizadas conforme o projeto avançar.

Como implantar o ambiente :arrow_forward:
Configurar as VLANs (3, 5, 8 e 10) na matriz;
Configurar os firewalls da matriz e da filial (controle de navegação e de acesso);
Estabelecer as VPNs em anel (matriz ↔ filial ↔ nuvem);
Provisionar o banco de dados na nuvem;
Publicar o servidor de aplicação na matriz;
Configurar acesso remoto com SSO/2FA ao servidor Windows;
Instalar os sensores e configurar o alarme e a coleta de logs.

Detalhar os comandos e configurações de cada etapa.

Como rodar os testes

Descrever os testes de validação (ex.: derrubar um link do anel e verificar a comunicação; testar bloqueio matriz ↔ filial; acionar sensor e validar alarme).

Aplicação de cadastro de clientes

Aplicação didática hospedada em servidor na matriz, com banco de dados na nuvem. O foco é a forma, o controle e a segurança do acesso (quem acessa e como), e não o design. Permite apenas o cadastro de clientes.

Casos de uso
Colaborador (VLAN5) acessa a aplicação para cadastrar clientes;
Equipe de TI (VLAN8) acessa os servidores em portas pré-definidas para manutenção;
Usuário remoto acessa o servidor Windows via SSO e dupla autenticação.
Iniciando/Configurando banco de dados

Inserir os comandos de criação e configuração do banco de dados na nuvem.

Tecnologias utilizadas :books:
Firewall (matriz e filial)
VPN site-to-site em anel
AWS / Azure
Servidor Windows com SSO e 2FA
Sensores PIR, LDR e ultrassônico
VLANs

Complementar com marcas, versões e linguagem da aplicação quando definidas.

Resolvendo Problemas :exclamation:

Em issues serão registrados os problemas gerados durante o desenvolvimento e como foram resolvidos.

Tarefas em aberto

:memo: Definir e documentar a topologia final

:memo: Implementar a VPN em anel e testar a redundância

:memo: Configurar SSO e dupla autenticação

:memo: Integrar sensores, alarme e gerenciamento de logs

:memo: Desenvolver a aplicação de cadastro de clientes

Desenvolvedores :octocat:
<img src="https://avatars.githubusercontent.com/u/0?v=4" width=115><br><sub>Nome</sub>	<img src="https://avatars.githubusercontent.com/u/0?v=4" width=115><br><sub>Nome</sub>
Licença

The MIT License (MIT)

Copyright :copyright: 2026 - Projeto Rede de Dados (SENAI e CTI)
