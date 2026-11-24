# Scripts

Scripts usados no projeto, versionados aqui para o grupo reproduzir o ambiente.

| Script | Onde roda | Função |
|--------|-----------|--------|
| `emulador.sh` | VM `iot-matriz` (`/opt/iot/emulador.sh`) | Emula o dispositivo IoT e envia leituras por MQTT a cada 5 segundos |
| Criação dos dashboards | SRV-LINUX | Cria os dashboards do Zabbix pela API; pode ser executado de novo |

Antes de adicionar um script, remova senhas, tokens e chaves. O `emulador.sh` já pede a senha do MQTT na execução, sem gravá-la. Contexto em [../docs/04-monitoramento-iot.md](../docs/04-monitoramento-iot.md).
