# 🔗 VPN e redundância

[⬅ Voltar ao README](../README.md)

A topologia utiliza túneis IPsec site-to-site em IKEv2.

| Túnel | Origem | Destino | Faixa |
|---|---|---|---|
| VPN-FIL-MEIO | Filial | Meio | 172.31.0.0/30 |
| VPN-MTZ-MEIO | Matriz | Meio | 172.31.0.12/30 |
| Matriz ↔ Nuvem | Matriz | AWS | 169.254.10.x |
| Filial ↔ Nuvem | Filial | AWS | 169.254.11.x |

## Situação atual

- A comunicação WAN entre matriz e meio responde.
- A VPN Matriz–Meio permanece em `connecting`.
- O meio recebe o `IKE_SA_INIT`, mas registra `no policy configured`.
- A filial ainda depende da ativação da WAN para criação do túnel.

## Próximo passo

Aplicar e validar a interface do túnel e as políticas no firewall do meio.
