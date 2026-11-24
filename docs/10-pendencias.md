# Pendências

Base: registros de 10/11/2026. A situação de cada requisito está em [01-requisitos.md](01-requisitos.md).

| Frente | Pendência | Observação |
|--------|-----------|------------|
| Firewall | Aplicar interface do túnel e políticas no meio e confirmar o túnel matriz–meio | [Diagnóstico](08-vpn-diagnostico.md) |
| Firewall | Criar políticas de servidores/IoT da matriz para a interface `VPN-MTZ-MEIO` | Sem elas nenhum tráfego passa pelo túnel |
| Firewall | Link e IP da `lan2` da filial; montar o túnel `VPN-FIL-MEIO` | Depende da CTI: [opções](07-vpn.md#wan-da-filial) |
| Firewall | Aprovar a faixa 172.31.0.12/30 (meio ↔ matriz) | Proposta do grupo |
| Firewall | Configurar os túneis com a nuvem (AWS) | Faixas 169.254.x.x já definidas |
| Firewall | Verificar a configuração de DNS do FortiGate da matriz e a rota padrão | [Conectividade](06-firewalls.md#conectividade-com-a-internet) |
| Firewall | Confirmar o modelo do firewall da filial | |
| Firewall | Confirmar os IPs de gerência (192.168.100.x) e os IPs da `lan1` (192.168.200.x) dos firewalls | Documentos de origem listam os dois conjuntos |
| Firewall | Definir o tempo de convergência do anel | |
| Firewall | Pedir à CTI reserva de DHCP para a `lan2` da matriz e a `port1` do meio | Evita que o túnel caia se o IP mudar |
| Firewall | Navegação da filial pelo firewall da matriz | Exigência do briefing |
| Firewall | Liberar até 10.0.3.10 as portas do AD para as VLANs 5 e 8 | DNS, Kerberos, LDAP, SMB, RPC e tempo |
| Switch | Definir a função das portas Gi1/0/1 a Gi1/0/5 do switch da filial | |
| Switch | Definir a VLAN dos PCs da filial e a separação da rede IoT | |
| Switch / Firewall | Configurar o link entre o switch e o firewall da filial | |
| Switch | Decidir sobre acesso remoto por SSH no switch da filial | |
| Switch | Conferir e salvar a configuração do switch da filial | |
| Servidores | Banco de dados na nuvem (AWS) e aplicação usando esse banco | VPC 10.1.1.0/24; hoje o banco é local |
| Servidores | Estrutura do AD: OUs, grupos e usuários de TI e colaboradores | Destrava a restrição do login do Linux e do RDP à TI |
| Servidores | Acesso remoto ao Windows com SSO e dupla autenticação | Diferencial do briefing |
| Servidores | Monitorar o SRV-WIN no Zabbix | Agente do Windows e template `Windows by Zabbix agent` |
| Servidores | DNS único: integrar o AD e o DNS do Kali | Hoje são duas zonas separadas |
| Servidores | Serviço systemd para o emulador IoT subir sozinho | Hoje roda à mão |
| Servidores | Teste de reinicialização de todas as VMs e do switch | Confirma a persistência das rotas e dos serviços |
| Servidores | Painéis do Grafana | Opcional; já está instalado |
| Rede / Servidores | Monitorar switch e firewall por SNMP | Itens no Zabbix |
| Rede / Servidores | Remover as placas temporárias de internet (SRV-LINUX, SRV-WIN e IoT) | Quando o firewall entregar a saída de internet |
| Rede / Servidores | Filial completa, com IoT e alarme, e host `IOT-FILIAL` no Zabbix | O alarme precisa soar nos dois escritórios |
| VPN | Anel de VPN entre matriz, filial e nuvem | Falta o firewall do meio e a nuvem |
| Todas | Itens do [checklist de segurança](09-seguranca.md#checklist-antes-da-entrega) | Antes da entrega |
