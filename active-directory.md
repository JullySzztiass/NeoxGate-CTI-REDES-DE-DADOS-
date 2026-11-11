# 🔐 Active Directory

[⬅ Voltar ao README](../README.md)

O Windows Server 2025 (`SRV-WIN`, `10.0.3.10`) atua como controlador de domínio de `ad.neoxgate.lab`.

## Configuração atual

- DNS do domínio executado no próprio SRV-WIN.
- SRV-LINUX ingressado no domínio com `realm join` e `sssd`.
- SSH ativo no SRV-WIN para administração remota.
- SYSVOL e NETLOGON compartilhados.
- O acesso ao Linux por usuários do domínio ainda não está restrito ao grupo da TI.

## Pendência

A estrutura de OUs, grupos e usuários ainda precisa ser definida para restringir adequadamente os acessos.
