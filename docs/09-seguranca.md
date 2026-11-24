# Segurança

## Controles aplicados

- Mosquitto sem acesso anônimo, com permissões separadas para o sensor (só publica) e para a ponte (só lê).
- Credenciais da ponte em arquivo com permissão restrita, fora do script.
- Aplicação liberada somente para as VLANs 5 e 8.
- IoT em VLAN própria, alcançando o servidor apenas em 1883/TCP e 514/UDP (mais PING).
- Segmentação por VLAN com regras entre elas no firewall da matriz ([políticas](06-firewalls.md#políticas-de-firewall)).
- Nenhuma política liga os PCs da matriz aos PCs da filial; só servidores e IoT atravessam os túneis.

## Limitações a documentar

Devem constar na entrega final.

| Limitação | Impacto |
|-----------|---------|
| A aplicação não tem login | Os logs do Apache registram só o IP de origem, não quem acessou (impacto na LGPD) |
| A senha do MQTT aparece na lista de processos (`ps`) enquanto a ponte e o emulador rodam | Exposição a quem tem acesso ao servidor |
| Acesso ao console do ESXi e ao menu de boot (GRUB) das VMs dá controle total sobre elas | O acesso ao ESXi precisa ser restrito |
| Disco do servidor em RAID 0 | Sem redundância: manter cópias das VMs fora do servidor |
| Criptografia DES nos túneis do laboratório | Ver [diferenças em relação à produção](07-vpn.md#diferenças-entre-laboratório-e-produção) |

## Checklist antes da entrega

- [ ] Trocar todas as senhas de teste: MariaDB do Zabbix, Mosquitto (`iot_sensor` e `zbx_bridge`), Admin do Zabbix, admin do Grafana, SSH e contas das VMs
- [ ] Revogar o token da API do Zabbix usado para criar os dashboards
- [ ] Tirar da regra do Apache o IP de teste `172.16.32.37`
- [ ] Restringir a interface do Zabbix e a porta 3000 do Grafana à VLAN 8 e ao próprio servidor
- [ ] Desconectar as placas temporárias de internet e conferir com `ip -br a` que o servidor usa só a rede do projeto
- [ ] Desligar o SSH do ESXi e do SRV-WIN, se não forem mais usados, e trocar a senha da iDRAC
- [ ] Colocar senha nas linhas `vty` do switch Cisco (hoje `login` sem senha)
- [ ] Nunca publicar o `show running-config` do switch: ele contém o hash do `enable secret`
- [ ] Conferir que nenhuma senha ou token está em arquivo versionado
