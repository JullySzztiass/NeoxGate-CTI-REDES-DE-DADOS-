1.Configuração do switch virtual
Foi criada uma máquina virtual com Debian 12, sem interface gráfica e com disco reduzido, para exercer a função de switch.
Foram criados cinco LAN Segments, que representam os cabos de rede do ambiente: link-fw, vlan3-srv, vlan5-colab, vlan8-ti e vlan10-iot.
A VM recebeu seis placas de rede, e a correspondência entre os nomes das interfaces e os segmentos foi conferida pelos endereços MAC.
Foi instalado o Open vSwitch e criada a bridge br0 com as seguintes portas:
Porta	Função
ens37	Trunk para o firewall (sem tag)
ens38	VLAN 3
ens39	VLAN 5
ens40	VLAN 8
ens41	VLAN 10
Foi configurada a ativação automática das placas a cada inicialização da VM.
2. Testes de funcionamento do switch

Foram criadas duas VMs de teste por meio de clones linkados, para economizar espaço em disco. Os testes realizados foram:

VLANs diferentes: as máquinas, mesmo na mesma faixa de IP, não se comunicaram, comprovando o isolamento.
Mesma VLAN: após alterar a tag da porta, as máquinas passaram a se comunicar normalmente, e a tag foi restaurada em seguida.

O switch foi considerado funcional.

3. Simulação do dispositivo IoT
Foi criada uma VM na VLAN 10 com o IP 10.0.10.10, simulando uma câmera.
Foi iniciado um servidor web em Python na porta 8080 para representar o serviço da câmera.
O acesso foi validado por ping e curl a partir de outra máquina da mesma VLAN.
Durante o processo, foram resolvidos problemas de layout de teclado e de instalação do curl, que exigiu ligar temporariamente a placa em NAT.

The [MIT License]() (MIT)

Copyright :copyright: 2026 - Projeto Rede de Dados (SENAI e CTI)

> Referência: [Preparação pré-projeto](https://miro.com/app/board/uXjVHhTdmiQ=/?share_link_id=182961988823)
