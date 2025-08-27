# btc-alert-mama
Automatización que envía alertas por Telegram cuando el precio de Bitcoin baja

# Descripción

Hice una automatización creada con [Make](https://www.make.com/) que envía una alerta automática por Telegram cuando el precio de Bitcoin baja más de un porcentaje definido en relación a un valor de referencia.

El objetivo no fue solo experimentar con herramientas de automatización, sino también resolver un problema real: ayudar a mi mamá a comprar Bitcoin para el largo plazo sin necesidad de seguir las cotizaciones en tiempo real.

Este proyecto consulta periódicamente la API de [CoinMarketCap](https://coinmarketcap.com/api/), compara el precio actual de BTC con un valor de referencia y, si detecta una baja mayor al **5%**, dispara un mensaje a través de un bot de Telegram.

El resultado es un sistema de alertas simple pero adaptable, que puede replicarse para múltiples usos: recordatorios de hábitos, métricas de negocio, seguimiento de precios, entre otros.

---

## Herramientas que necesitas

- **Make** → para la lógica de automatización y conexión entre servicios.
- **CoinMarketCap API** → para obtener el precio actual del BTC.
- **Telegram Bot** → para enviar las notificaciones.

---

## Cómo funciona el flujo

1. **Trigger**: el escenario corre cada 1 hora en Make.
2. **API Call**: obtiene el precio actual de BTC en USD desde CoinMarketCap.
3. **Variables**:
    - Precio de referencia (definido manualmente).
    - Precio actual.
    - Porcentaje de variación.
4. **Filtro**: si la variación es igual o mayor a -5%, continúa.
5. **Acción**: envía un mensaje por Telegram al destinatario configurado.
