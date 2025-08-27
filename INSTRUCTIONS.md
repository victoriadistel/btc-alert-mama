## Detalle paso a paso si queres probarlo

1. Crear una cuenta en [Make](https://www.make.com/) si no tenes una.
  - Debés crear un nuevo scenario.
  - Si no tenes cuenta en CoinMarketCap deberás crearte una para poder obtener la API key de CoinMarketCap (https://coinmarketcap.com/api/)
  - La API key es algo como: "a1z5c3d4-e5t9-7330-qycd-ju6945832760". Deberás copiarla.
  
2. Cuando tenemos la cuenta de Make y la API Key de CoinMarketApp empezamos a armar el scenario:
  - Un scenario se empieza agregándole módulos, en este caso, empezamos añadiendo un módulo de CoinMarketCap --> Make an API Call
    - En el campo URL deberás pegar: v1/cryptocurrency/quotes/latest para que encuentre las cotizaciones.
    - Method --> GET
    - Headers
        Item 1
          Key:
            [Content-Type]
          Value:
            [application/json]
    + Add item - Item 2
          Key:
            [X-CMC_PRO_API_KEY]
          Value:
            [tu_API_KEY_de_CoinMarketCap]
    - Query String
    + Add item - Item 1
          Key:
            [symbol]
          Value:
            [BTC]
        Item 2
          Key:
            [convert]
          Value:
            [USD]
  - El body se deja vacío y pone Save para guardar.

3. Si eso funciona, segumos agregándole mas módulos en el + --> Add another module
  - Elegmimos Tools --> Set multiple variables
    - Variables
      + Add item - Item 1
          Variable name:
            [reference_price]
          Variale value:
            [el_precio_que_uses_de_referencia]
      + Add item - Item 2
          Variable name:
            [current_price]
          Variale value:
            [formatNumber(1.body.data.BTC.quote.USD.price; ; ; )] 
            // Acá mapeamos el precio que traemos de CoinMarketCap [body --> data --> BTC --> quote --> USD --> price] 
            y lo formateamos para que marque bien los decimales.
      - Save
    - //Este paso es muy importante!! Vamos a setear la variable [percentage_change] en un módulo diferente porque para usarla
    necesitamos las variables base que son [reference_price] y [current_price]. Entonces:
    - Add another module --> Tools --> Set variable
      Variable name:
        [percentage_change]
      Variale value:
        [round(((3.reference_price - 3.current_price) / 3.reference_price) * 100)]
        // Acá usamos las variables base y hacemos el cálculo del porcentaje que cambió con respecto al precio de referencia
        y el precio actual. 
    - Save

  4. Ya terminamos con las variables. Seguimos con el Bot de Telegram. Antes de seguir con el módulo, vamos a crear el Bot
    - Abrí Telegram y busca @botfather
      - Mandale un mensaje diciendo /start
        - Mandale un mensaje con la opción /newbot
          - Elegí el nombre que quieras darle a tu bot y mandáselo. Por ejemplo, el mio es [BTCAlertBot]
            - Elegí un username para tu bot y mandáselo, ejemplo: [btc_alert_mama_bot]
      - Botfather te va a devolver un token para acceder a la HTTP API, algo como: [1658943780:ABCdefGhIJKlmNoPQRsTuVwXyZ]
      - Buscá el chat con el bot que creaste [btc_alert_mama_bot] y mandale /start.
      // El token te va a servir para poder acceder a tu Chat ID y vincular el bot con Make
      - Para obtener tu Chat ID andá a https://api.telegram.org/bot[TU_TOKEN]/getUpdates
        - En [TU_TOKEN] pegá el token que te dio botfather
        - Buscá donde dice ["chat": {"id": 555712688,... }], ESE es el número de tu Chat ID y volvemos a Make.
    - Add another module --> Telegram Bot --> Send a Text Message or a Reply
      - Conneciton --> Add
          Conenction name:
          - Elegí el nombre que quieras ponerle [TelegramAlertBtc]
          Token: 
            [TU_TOKEN]
          - Save
      - Chat ID:
        [555712688]
      - Text:
        "💰 ¡OPORTUNIDAD BITCOIN!

        🎯 El precio bajó [percentage_change]%
        💵 Precio actual: $[current_price]
        📉 Desde: $[reference_price]
        📊 Tendencia de 7 días: [body.data.BTC.quote.USD.percent_change_7d] %
        
        💡 Puede ser buen momento para comprar gradualmente.
        ⏰ Fecha: formatDate(now; "DD/MM/YYYY HH:mm:ss")
        
        ℹ️ Esto es una alerta personalizada. Si tenés dudas, consultalo con tu hija Vicky😘"
        // Obviamente acá podes personalizar el texto como quieras, incluso incluir cualquier variable de las que ofrece
        CoinMarketCap.
        // Tene en cuenta que acá lo explique como si fuera mi usuario, pero al ser para mi mamá deberás hacer que el usuario
        al que quieras que le envíe la alerta empiece una conversación con el bot que creaste buscando el nombre del bot e iniciando
        una conversación. Al abrir [https://api.telegram.org/bot[TU_BOT_TOKEN]/getUpdates] te dara los datos con su Chat ID.
      - Save
      
5. Por último, como no quiero que el Bot bombardee a mi mamá con alertas todo el tiempo, en la llavecita que está entre 
    Tools ----- Telegram Bot vamos a crear un filtro:
    - Set up a filter
      Label:
      [el_nombre_que_quieras]
        Condition:
          [percentage_change]
          greater than or equal to
          [5]
      - Save
      // Esto también es personalizable, yo establecí que cuando el porcentaje de cambio sea igual o mayor a 5%, envie la 
      alerta. Si el porcentaje de variación entre el precio de referencia y el precio actual es menor a 5, considero en mi caso
      que no se justifica enviarle una alerta a mi mamá.
      
6. Si todos los pasos previos funcionan, le damos un Save a todo el scenario en la bara que herrameintas que está debajo del lienzo
  - La versión gratuita de Make permite correr el scenario cada 15 minutos como mínimo, yo lo setee cada 60 minutos. Libre de elección.
  - Le damos al botón de Run y ya está listo.
  - Si no tiró algún error y al darle run no te llegó anda es porque probablemente ande bien. Cambiale el valor a la variable current_price
  o modificá el filtro a un valor que sepas que te va a enviar a la alerta y ¡voilá!
  
## Algunas consideraciones a tener en cuenta

- Es importante que cuando querramos usar variables o "items" como [percentage_change] o [body.data.BTC.quote.USD.percent_change_7d]
  no copies el texto, sino que las elijas desde las opciones que te da Make, sino no las toma.
- Aún no lo probe, pero si quisieramos que el mensaje se enviase a más de un usuario creo que bastaría con duplicar el módulo de
  Telegram, cambiando el Chat ID al de cada destinatario.
- Todo es personalizable. Yo utilicé esas variables, pero hay libertad de elecicón según la finalidad, el precio de referencia
  e incluso de la estrategia de inversión que quieras hacer.

## Posibles upgrades

- Guardar precios históricos para comparar tendencias.  
- Adaptarlo a múltiples monedas o activos (ETH, acciones)
- Configurar alertas variables según usuario.
- Enviar resúmenes diarios/semanales además de alertas.

# Dejo capturas del flujo en:
- 1er_paso.png --> módulo CoinMarketCap
- 2do_paso.png --> módulo variables base
- 3er_paso.png --> módulo variable percentage_change
- 4to_paso.png --> módulo Telegram Bot
- 5to_paso.png --> filtro del envío de la alerta
- scenario_btc_alerta_mama.png --> muestra el flujo completo
- mensaje_alerta_btc.png --> mensaje de alerta enviado
