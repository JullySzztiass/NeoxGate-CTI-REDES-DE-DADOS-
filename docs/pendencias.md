# Pendências

Base: documentação de 10/11/2026. A situação de cada requisito está em [requisitos.md](requisitos.md); os itens de endurecimento de segurança estão em [seguranca.md](seguranca.md#checklist-antes-da-entrega).

## Servidores

| Pendência | Observação |
|-----------|------------|
| Banco de dados na nuvem (AWS) e aplicação usando esse banco | VPC do projeto 10.1.1.0/24; hoje o banco é local |
| Estrutura do AD: OUs, grupos e usuários de TI e colaboradores | Destrava a restrição do login do Linux e do RDP à TI |
| Acesso remoto ao Windows com SSO e dupla autenticação | Diferencial do briefing |
| Monitorar o SRV-WIN no Zabbix | Agente do Windows e template `Windows by Zabbix agent` |
| DNS único: integrar o AD e o DNS do Kali | Hoje são duas zonas separadas |
| Serviço systemd para o emulador IoT subir sozinho | Hoje roda à mão |
| Painéis do Grafana | Opcional; já está instalado |
| Teste de reinicialização de todas as VMs e do switch | Confirma a persistência das rotas e dos serviços |

## Rede

| Pendência | Observação |
|-----------|------------|
| Estabelecer o túnel matriz–meio | Aplicar interface e políticas no meio; ver [diagnóstico](rede-firewall-vpn.md#diagnóstico-do-túnel-matriz-meio) |
| Aplicar na matriz as políticas com a interface `VPN-MTZ-MEIO` | Sem elas nenhum tráfego passa pelo túnel |
| WAN da filial (`lan2`) e túnel `VPN-FIL-MEIO` | Depende da CTI: IP fixo, liberação de MAC ou cabo direto |
| VPN em anel entre matriz, filial e nuvem | Falta o firewall do meio e a nuvem |
| Filial completa, com IoT e alarme, e host `IOT-FILIAL` no Zabbix | O alarme precisa soar nos dois escritórios |
| Navegação da filial pelo firewall da matriz | Exigência do briefing |
| Monitorar switch e firewall por SNMP | Itens no Zabbix |
| Remover as placas temporárias de internet (SRV-LINUX, SRV-WIN e IoT) | Quando o firewall entregar a saída de internet |
| Liberar no firewall as portas do AD até 10.0.3.10 | Para as VLANs 5 e 8 entrarem no domínio |
| Pedir à CTI reserva de DHCP para `lan2` da matriz e `port1` do meio | Evita que o túnel caia se o IP mudar |

## Pontos a alinhar entre as frentes

Divergências entre os dois documentos de origem; o grupo precisa definir qual vale antes da entrega.

| Ponto | Documento de servidores | Documento de VPN |
|-------|-------------------------|------------------|
| Endereços dos firewalls | Matriz 192.168.100.10, filial 192.168.100.20 e meio 192.168.100.1 | Matriz `lan1` 192.168.200.10, filial `lan1` 192.168.200.1; WAN em 172.16.31.0/24 |
| Faixas dos túneis | 172.31.0.0/30, 172.31.0.4/30 e 172.31.0.8/30 (matriz–filial, matriz–nuvem, filial–nuvem) | 172.31.0.0/30 (filial–meio) e 172.31.0.12/30 (meio–matriz, proposta); nuvem em 169.254.x.x |
| Faixa 172.31.0.12/30 | — | Proposta nova, depende da aprovação do grupo |

As tabelas de [arquitetura.md](arquitetura.md) seguem o documento de VPN, mais recente para essa parte.
