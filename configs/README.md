# Configurações

| Arquivo | Equipamento | Situação |
|---------|-------------|----------|
| [fortigate-matriz.txt](fortigate-matriz.txt) | Firewall da matriz | Aplicado |
| [fortigate-meio.txt](fortigate-meio.txt) | Firewall do meio | Fase 1 e 2 verificadas; restante sem confirmação |
| [fortigate-filial-modelo.txt](fortigate-filial-modelo.txt) | Firewall da filial | Modelo, não aplicado |

Regras para esta pasta:

- Senhas pré-compartilhadas ficam como placeholder (`<SENHA-FORTE-1>`); a senha real nunca entra no repositório.
- Não publicar `show running-config` do switch Cisco: ele contém o hash do `enable secret`. Se for incluir a configuração dos switches, sanitize antes.
- Contexto de cada bloco em [../docs/07-vpn.md](../docs/07-vpn.md) e [../docs/06-firewalls.md](../docs/06-firewalls.md).
