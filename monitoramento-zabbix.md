# 📊 Monitoramento com Zabbix

[⬅ Voltar ao README](../README.md)

O Zabbix 7.0 monitora o dispositivo IoT e os serviços do SRV-LINUX.

## Hosts

| Host | Itens principais |
|---|---|
| IOT-MATRIZ | PIR, LDR, distância e alarme |
| Zabbix server | Syslog, Mosquitto, Apache, ponte e logs |

## Triggers

| Trigger | Condição |
|---|---|
| Alarme ativo | `sensor.alarme` igual a 1 |
| Movimento fora do expediente | PIR ativo fora do horário |
| Presença próxima | Distância menor que 30 cm fora do horário |
| Luz acesa | LDR alto fora do horário |
| Sensor sem dados | Sem valor de PIR por 2 minutos |
| Serviço parado | Porta ou processo indisponível |

## Dashboards

- **IoT - Matriz:** sensores, disponibilidade e problemas.
- **Infraestrutura:** estado dos serviços e eventos de acesso.
