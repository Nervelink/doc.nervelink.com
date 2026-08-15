# Datagrama (/docs/red/v1/referencia/api/datagrama)



# Datagrama [#datagrama]

`Datagrama` representa una unidad de datos asociada al transporte no orientado a conexión. En Eco aparece como una pieza interna de la ruta UDP, por debajo del nivel de `Paquete`.

```text
Paquete
  ↓
Buffer
  ↓
Datagrama
  ↓
UDP
```

## UDP frente a TCP [#udp-frente-a-tcp]

Un stream TCP entrega un flujo ordenado de bytes. Un datagrama conserva los límites de cada unidad de envío.

Esto permite que el consumidor UDP pueda procesar mensajes independientes sin reconstruir un stream TCP.

## Uso en Eco [#uso-en-eco]

La mayor parte del código de juego no necesita crear `Datagrama` manualmente. El protocolo UDP y sus estructuras auxiliares lo gestionan internamente.

## Relación con Paquete [#relación-con-paquete]

`Paquete` responde a **qué operación es ésta**.

`Datagrama` responde a **cómo se transporta esta unidad de bytes por UDP**.

Esta separación permite transportar el mismo modelo de protocolo sin introducir reglas de gameplay en la capa de sockets.

<Callout title="API de infraestructura" type="warn">
  Un `Datagrama` es una abstracción de transporte. Si estás implementando una RFC, sincronización o instancia de objeto, debes trabajar en una capa superior.
</Callout>

## Diagnóstico UDP [#diagnóstico-udp]

Cuando un mensaje UDP no llega, revisa:

```text
Puerto UDP
   ↓
Activación UDP
   ↓
Datagrama recibido
   ↓
Buffer
   ↓
Paquete
   ↓
Handler
```

No confundas un problema de datagrama con una ausencia de `Objeto` o una falta de autoridad.

<Card title="TCP y UDP" href="/docs/red/v1/transporte/tcp-udp">
  Comparación de los transportes de Eco.
</Card>

<Card title="Paquete" href="/docs/red/v1/referencia/api/paquete">
  Unidad lógica del protocolo.
</Card>
