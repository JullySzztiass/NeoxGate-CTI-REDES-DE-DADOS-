# Switches

## Matriz · `SW-MATRIZ`

`SW-MATRIZ` (Cisco WS-C2960S-24TD-L), com as VLANs 3, 5, 8 e 10 criadas. Substitui o switch virtual em Open vSwitch usado no protótipo.

| Porta | Função | Configuração |
|-------|--------|--------------|
| Gi4/0/1 | Uplink do firewall da matriz | Trunk, VLANs 3, 5, 8 e 10 |
| Gi4/0/2 | Servidor ESXi | Trunk, VLAN 3 nativa, VLANs 3 e 10 permitidas |
| Gi4/0/3 | Colaboradores | Acesso, VLAN 5 |
| Gi4/0/4 | TI | Acesso, VLAN 8 |
| Gi4/0/5 | IoT físico | Acesso, VLAN 10 |

O switch separa o tráfego por função: VLAN 3 (servidores), VLAN 5 (colaboradores), VLAN 8 (TI) e VLAN 10 (IoT).

## Filial · `SW-FILIAL-CTI`

Cisco Catalyst 2960-S (`SW-FILIAL-CTI`). A configuração inicial já foi aplicada, mas é **provisória**: as conexões e as VLANs ainda precisam ser validadas antes de o equipamento ser considerado pronto.

| Item | Valor |
|------|-------|
| Modelo | Cisco Catalyst 2960-S |
| Hostname | `SW-FILIAL-CTI` |
| Acesso de gerência | Console, com usuário local `admin` |
| Status | Configuração inicial provisória |

### O que foi configurado

| Item | Configuração |
|------|--------------|
| Identificação | Hostname `SW-FILIAL-CTI` |
| Acesso privilegiado | Senha de enable definida |
| Autenticação | Usuário local `admin` e login local no console |
| Senhas | Criptografia de senhas ativada na configuração |
| Banner | Banner de acesso restrito |
| Console | Timeout de inatividade de 5 minutos |
| VLAN | VLAN 10 criada com o nome `IOT2`, ainda sem portas atribuídas |

### Portas

| Portas | Estado | Observação |
|--------|--------|------------|
| Gi1/0/1 a Gi1/0/5 | Disponíveis | Função de cada porta ainda a definir |
| Gi1/0/6 a Gi1/0/48 | Desativadas (`shutdown`) | Portas não utilizadas |
| Te1/0/1 e Te1/0/2 | Sem alteração | Portas de 10 Gb |
| Fa0 | Sem alteração | Porta de gerenciamento |

As portas não utilizadas permanecem desativadas para reduzir a superfície de acesso à rede.

**Pendências do switch da filial:** função das cinco portas, VLAN dos PCs e separação da rede IoT, link com o firewall, decisão sobre SSH e conferência e gravação da configuração. A lista completa está em [10-pendencias.md](10-pendencias.md).
