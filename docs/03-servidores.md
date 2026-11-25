# Servidores

> **Escopo:** infraestrutura física, ESXi, Active Directory e aplicação. Consulte [arquitetura e endereçamento](02-arquitetura.md) para IPs e [monitoramento e IoT](04-monitoramento-iot.md) para Zabbix, MQTT e syslog.

## Servidor físico e ESXi

Os três servidores do projeto rodam como VMs em um Dell PowerEdge T440 com VMware ESXi 8.0 U3e.

| Item | Valor |
|------|-------|
| Processador | 8 CPUs, Intel Xeon Bronze 3106 a 1,70 GHz |
| Memória | 15,56 GB |
| Armazenamento | `datastore1`, 150,75 GB, RAID 0 (sem redundância) |
| Instalação | Pela iDRAC, com a ISO montada como mídia virtual |

**Rede.** O servidor tem duas portas, cada uma em um vSwitch:

| Placa | vSwitch | Destino |
|-------|---------|---------|
| vmnic0 | vSwitch0 | Rede da escola: acesso ao ESXi e internet temporária das VMs |
| vmnic1 | vSwitch-lab | Switch Cisco da matriz, porta Gi4/0/2 (trunk) |

O `vSwitch-lab` tem os port groups `Vlan3-SRV` (ID 0, VLAN 3 chega sem tag como nativa) e `Vlan10-iot` (ID 10, com tag).

**Máquinas virtuais.**

| VM | Sistema | Recursos | Rede |
|----|---------|----------|------|
| SRV-Windows25 - NeoxGate | Windows Server 2025 | 2 vCPU, 6 GB, disco de 90 GB em Thin (cerca de 28 GB usados) | `Vlan3-SRV` e `VM Network` (placa temporária) |
| SRV-LINUX - NeoxGate | Debian 12 | 2 GB | `Vlan3-SRV` e `VM Network` (placa temporária) |
| IoT - NeoxGate | Debian 12 | 2 vCPU, 2 GB, disco de 8 GB | `Vlan10-iot` e `VM Network` (placa temporária) |

As placas temporárias na `VM Network` dão internet às VMs e serão removidas quando o firewall entregar a saída (ver [pendências](10-pendencias.md)). A VM `DOCKER-TOMCAT` que aparece no ESXi pertence a outro grupo.

O disco do Windows foi migrado de Thick para Thin exportando e importando a VM com o `ovftool`, liberando cerca de 65 GB.

## Active Directory

O `SRV-WIN` (Windows Server 2025, 10.0.3.10) é o controlador de domínio único de `ad.neoxgate.lab` (NetBIOS `NEOXGATE`). O DNS do domínio roda no próprio servidor, com registros do domínio, do `srv-win` e do `srv-linux`.

- A placa de internet temporária do Windows fica desativada: o controlador de domínio registraria o IP dela no DNS e os clientes de outras VLANs receberiam o endereço errado.
- O SSH está ativo para administração remota.

**Ingresso do Linux no domínio.** O `SRV-LINUX` entrou com `realm join` e `sssd`. O `/etc/resolv.conf` precisa apontar para 10.0.3.10, porque a placa temporária de internet reescreve o arquivo a cada reinicialização.

Hoje qualquer usuário do domínio pode entrar no Linux (`allow-realm-logins`). Restringir ao grupo da TI depende de criar OUs e grupos ([pendências](10-pendencias.md)).

## SRV-LINUX

Debian 12 (`srv-linux.ad.neoxgate.lab`) concentra aplicação, banco, MQTT, syslog e monitoramento na VLAN 3.

| Serviço | Função | Porta |
|---------|--------|-------|
| Apache e PHP | Aplicação de cadastro (`/clientes`) e interface do Zabbix (`/zabbix`) | 80 e 443 |
| MariaDB | Banco da aplicação (`appclientes`, usuário próprio) e banco do Zabbix | 3306, só local |
| Mosquitto | Broker MQTT com usuário e senha, sem acesso anônimo | 1883/TCP |
| Ponte `mqtt2zabbix` | Serviço systemd que lê o Mosquitto e entrega os valores ao Zabbix | — |
| Zabbix server | Armazena dados, avalia triggers e gera alertas | 10051/TCP |
| Zabbix Agent 2 | Monitora o servidor e lê arquivos de log | 10050/TCP |
| rsyslog | Syslog central | 514/UDP e TCP |
| dnsmasq | DNS local com os nomes do laboratório | 53 |
| Grafana | Instalado com o plugin do Zabbix, ainda sem painéis | 3000/TCP |

## Aplicação de cadastro

Roda no Apache em `/clientes` e permite o cadastro de clientes no banco `appclientes` (MariaDB, usuário próprio). O banco é local; a migração para a nuvem está nas [pendências](10-pendencias.md).

**Controle de acesso.** O arquivo `appclientes.conf` do Apache usa `Require ip` para liberar somente as VLANs 5 e 8 e o próprio servidor. A VLAN 3 recebe `403 Forbidden`, como o briefing pede. A prova está no acesso feito a partir do `SRV-WIN` (VLAN 3); para demonstrar a aplicação funcionando, o acesso precisa vir de uma máquina das VLANs 5 ou 8.

**Limitação.** A aplicação não tem login, então os logs do Apache registram só o IP de origem e não quem acessou. Detalhes e impacto na LGPD em [seguranca.md](09-seguranca.md#limitações-a-documentar).

## Testes realizados

| Teste | Evidência |
|-------|-----------|
| Saúde do AD | `dcdiag` nos testes de conectividade, anúncio, NetLogon, serviços e FSMO; SYSVOL e NETLOGON compartilhados |
| DNS do AD | `nslookup ad.neoxgate.lab` devolve só 10.0.3.10 |
| Ingresso do Linux no domínio | `realm list` com `kerberos-member`; `Get-ADComputer` lista o SRV-LINUX |
| Controle de acesso da aplicação | O SRV-WIN (VLAN 3) recebe `403 Forbidden` em `/clientes` |
| Migração do Windows para Thin | Hash SHA256 igual ao do manifesto; VM importada com o AD funcionando |
