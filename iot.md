# 📡 IoT e fluxo dos dados

[⬅ Voltar ao README](../README.md)

O dispositivo IoT é emulado em uma VM Debian 12:

- **Hostname:** `iot-matriz`
- **IP:** `10.0.10.10`
- **Rede:** VLAN 10

O emulador envia leituras de PIR, LDR e distância por MQTT a cada 5 segundos.

## Cenários

| Cenário | Resultado |
|---|---|
| `normal` | Alarme 0 |
| `movimento` | PIR 1 e alarme 1 |
| `luz` | LDR alto e alarme 1 |
| `presenca` | Distância abaixo de 30 cm e alarme 1 |

## Fluxo

```text
IoT → MQTT 1883/TCP → Mosquitto → mqtt2zabbix → Zabbix
IoT → Syslog 514/UDP → rsyslog → Zabbix
```

O emulador não envia dados diretamente ao Zabbix.
