# 🛡️ Segurança e limitações

[⬅ Voltar ao README](../README.md)

## Controles aplicados

- Mosquitto sem acesso anônimo.
- Permissões separadas para sensor e ponte.
- Aplicação liberada apenas para VLANs 5 e 8.
- IoT isolado em VLAN própria.
- PCs da matriz e filial sem comunicação direta.

## Antes da entrega

- Trocar senhas de teste.
- Revogar tokens temporários.
- Remover regras e IPs usados apenas em testes.
- Desconectar interfaces temporárias de internet.
- Revisar acessos SSH e administrativos.
- Restringir Zabbix e Grafana.
- Não publicar senhas, tokens ou configurações com credenciais.

## Limitações

- A aplicação não possui login.
- O servidor utiliza RAID 0, sem redundância.
- O acesso ao ESXi precisa ser restrito.
- O ambiente de laboratório utiliza criptografia reduzida no firewall do meio.
