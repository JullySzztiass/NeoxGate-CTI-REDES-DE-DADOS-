# Diagnóstico da VPN matriz–meio

Registro da investigação de 10/11/2026. O meio recebe o pedido IKE da matriz e o descarta com `no policy configured`; rede, proposal e configuração do túnel foram descartados como causa. Contexto geral em [07-vpn.md](07-vpn.md).

## Cronologia

| Hora | Teste | Resultado | Conclusão |
|------|-------|-----------|-----------|
| 16:26 | `diagnose vpn ike gateway list` e ping ao túnel | `connecting`, SPI do responder zerado; 100% de perda | A matriz envia o `IKE_SA_INIT` e não recebe resposta |
| 16:29 | Ping pela WAN; phase1 do meio | Pings responderam; meio com `des-md5 des-sha1` | Rede OK; proposals diferentes (matriz com `aes256-sha256`) |
| 16:31 | phase2 do meio | Também `des-md5 des-sha1` | Phase2 divergente |
| 16:32 | `set proposal aes256-sha256` no meio | `command parse error`, código -61, no phase1 e no phase2 | O meio rejeita AES |
| 16:34 | `set proposal ?` no meio | Só `null-*` e `des-*` | Só criptografia fraca disponível |
| 16:38 | `des-sha256` nos dois lados, `clear` do gateway | Aplicado sem erro; `gateway list` vazio; ping falha | Túnel ainda não sobe |
| 16:40 | `gateway list` na matriz | `connecting`, SPI zerado | Sem resposta do meio |
| 16:41 | Sniffer na `port1` do meio, UDP 500 | Pacotes de 436 bytes da matriz chegam e se repetem; sem resposta | Rede e UDP 500 OK; o meio não responde |
| 16:42 | `diagnose debug application ike -1` no meio | `ignoring IKEv2 request, no policy configured`; proposta recebida: DES_CBC, SHA2_256, MODP2048 | O meio rejeita antes de comparar algoritmo ou senha |
| 16:43 | `show full-configuration` do phase2 do meio | Correto: `phase1name`, `des-sha256`, DH 14, seletores 0.0.0.0/0, auto-negotiate | Registro não está corrompido |
| 16:45 | `show full-configuration` do phase1 do meio | Correto: `des-sha256`, `remote-gw 172.16.31.30`, `port1`, sem `status disable` | Configuração do túnel está certa |

## Causa mais provável (não confirmada)

Em FortiGates recentes, o IKE de um túnel de interface parece ignorar o pedido quando nenhuma política de firewall referencia a interface, e o meio ainda não tinha política com `VPN-MEIO-MTZ`. Não há documentação que confirme esse comportamento nesta versão.

**Teste.** Aplicar no meio a interface do túnel e as políticas ([configs/fortigate-meio.txt](../configs/fortigate-meio.txt)) e rodar na matriz:

```bash
diagnose vpn ike gateway clear name VPN-MTZ-MEIO
diagnose vpn ike gateway list
```

Esperado: `status: established`, seguido de ping em 172.31.0.13 (da matriz) e 172.31.0.14 (do meio).

**Se continuar igual:**

1. Coletar `get system status` do meio e verificar `License Status` e `VM Resource`. A VM de avaliação oferecer só DES/NULL sugere limitação de licença, ainda não verificada.
2. Testar `set pfs disable` no phase2 do meio (hipótese menos provável).
3. Trocar o meio por um pfSense, configurável pela tela e sem dependência de licença.

A senha pré-compartilhada do meio aparece criptografada e não pode ser comparada visualmente. Se o túnel subir e falhar na autenticação, redefina `psksecret` nas duas pontas com a mesma senha.

## Filial: acesso pelo Kali à `lan1`

O Kali (`eth0` 192.168.200.50/24, VirtualBox) recebe `Destination Host Unreachable` ao pingar 192.168.200.1, e o firewall também não alcança o Kali (`get system arp` vazio). Como nenhum dos dois resolve o ARP do outro, o problema é de cabo, porta ou adaptador da VM, e não de regra de firewall. A `lan1` já permite ping, https, ssh, snmp e http.

A configuração da filial não depende do Kali: os blocos de CLI podem ser colados pelo console (COM4).

**Verificações, em ordem:**

1. No firewall: `diagnose sniffer packet lan1 'arp or icmp' 4 10` enquanto o Kali pinga. Se o `who-has` não aparece, o Kali não está na mesma rede física da `lan1`.
2. No Kali: `ip neigh show` e ping para 192.168.200.10 (`lan1` da matriz).
3. No VirtualBox: Adaptador 1 em modo bridge, na placa Ethernet física ligada ao firewall (não o Wi-Fi), com "Cabo conectado" e modo promíscuo em "Permitir tudo".
4. Cabo direto entre o notebook e a `lan1`, se houver switch no caminho.

## Comandos de verificação

| Objetivo | Comando | Onde |
|----------|---------|------|
| Ver IPs e link das interfaces | `get system interface physical` | Os três |
| Testar a WAN entre matriz e meio | `execute ping 172.16.31.43` / `execute ping 172.16.31.30` | Matriz / meio |
| Estado do túnel IKE | `diagnose vpn ike gateway list` | Matriz e meio |
| Reiniciar a negociação | `diagnose vpn ike gateway clear name VPN-MTZ-MEIO` | Matriz |
| Ver se o IKE chega | `diagnose sniffer packet port1 'host 172.16.31.30 and udp port 500' 4 20` | Meio |
| Depurar o IKE | `diagnose debug application ike -1` e `diagnose debug enable`; parar com `diagnose debug disable` e `diagnose debug reset` | Meio |
| Conferir phase1 e phase2 | `show full-configuration vpn ipsec phase1-interface <nome>` e `…phase2-interface <nome>` | Meio |
| Listar proposals aceitos | `set proposal ?` dentro do `edit` | Meio |
| Conferir a interface do túnel | `show system interface VPN-MEIO-MTZ` | Meio |
| Políticas e serviços | `show firewall policy`, `show firewall service custom` | Matriz |
| Seguir um fluxo (syslog) | `diagnose debug flow filter saddr 10.0.10.10`, `daddr 10.0.3.20`, `dport 514`, `diagnose debug flow trace start 20` | Matriz |
| ARP e camada 2 na filial | `get system arp`, `diagnose sniffer packet lan1 'arp or icmp' 4 10` | Filial |
| Licença e versão | `get system status` | Meio |
| Rotas e internet | `get router info routing-table all`, `execute ping 8.8.8.8` | Matriz |

## Evidências coletadas

Úteis como anexo do relatório: `gateway list` da matriz em `connecting`, sniffer do meio mostrando o IKE chegando, debug com `no policy configured`, `show full-configuration` do phase1 e do phase2 do meio, `show firewall policy` da matriz e `show firewall service custom`. Senhas pré-compartilhadas ficam fora do relatório.
