# TCP y UDP en profundidad (/docs/red/v1/transporte/tcp-udp-en-profundidad)



# TCP y UDP en profundidad [#tcp-y-udp-en-profundidad]

Eco utiliza TCP como transporte principal del cliente y contempla UDP para datos donde la pérdida o reordenación sea aceptable. `ClienteJuego` mantiene explícitamente ambos protocolos y expone si UDP puede utilizarse. fileciteturn216file0

## TCP [#tcp]

TCP es apropiado para operaciones que necesitan entrega fiable y ordenada:

* entrada y salida de canales;
* creación y destrucción de objetos;
* cambios de propiedad;
* datos persistentes;
* control de sesión.

La conexión TCP constituye el vínculo principal entre cliente y servidor.

## UDP [#udp]

UDP puede utilizarse para tráfico frecuente donde repetir o perder datos sea preferible a bloquear el flujo fiable. El cliente comprueba `canUseUDP` antes de asumir que está disponible. fileciteturn216file0

Ejemplos conceptuales:

```text
Posición / orientación de alta frecuencia → UDP posible
Handshake / creación de objetos           → TCP
Estado persistente                         → TCP
```

La elección depende de la semántica del dato, no de si el dato “parece rápido”.

## Activación [#activación]

El protocolo incluye `RequestSetUDP`, `ResponseSetUDP` y `RequestActivateUDP`. UDP no es simplemente otro socket que se usa sin negociación: existe una fase de activación asociada a la sesión. fileciteturn227file0

## NoDelay [#nodelay]

`ClienteJuego.noDelay` permite cambiar el comportamiento de Nagle en TCP. Activarlo puede reducir latencia a costa de aumentar tráfico, por lo que debe medirse con el patrón de mensajes real. fileciteturn216file0

## Conexión personalizada [#conexión-personalizada]

La arquitectura también permite utilizar una `IConnection` personalizada. Esto abre la puerta a transportes externos cuando el proyecto necesita una capa distinta del socket TCP/UDP tradicional.

## Regla de diseño [#regla-de-diseño]

No selecciones UDP para “hacerlo más rápido” sin definir primero qué sucede cuando un paquete llega tarde, duplicado o nunca llega. Si la lógica no puede tolerarlo, el dato necesita un flujo fiable o una estrategia explícita de corrección.

## Referencias [#referencias]

<Card title="Paquetes y protocolo" href="/docs/red/v1/transporte/paquetes-protocolo">
  Operaciones de protocolo relacionadas con TCP y UDP.
</Card>

<Card title="Cliente" href="/docs/red/v1/runtime/cliente">
  Ciclo de conexión y disponibilidad del transporte.
</Card>
