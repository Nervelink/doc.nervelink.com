# Modelo TCP y UDP (/docs/red/v1/transporte/modelo-tcp-udp)



# Modelo TCP y UDP [#modelo-tcp-y-udp]

Eco mantiene un transporte principal basado en TCP y una vía UDP opcional para tráfico donde la latencia importa más que la entrega garantizada de cada mensaje.

## TCP [#tcp]

TCP proporciona un flujo fiable y ordenado. Es la elección natural para operaciones donde perder o reordenar mensajes sería peor que esperar un poco más.

Ejemplos:

```text
Conectar
Unirse a canal
Crear / destruir objetos
Cambios de datos persistentes
RFC importantes
Operaciones administrativas
```

En Eco, `EnviarPaquete(buffer, fiable: true)` representa el camino fiable normal.

## UDP [#udp]

UDP permite enviar datagramas sin el coste de garantizar la entrega individual de cada uno. Eco expone `IniciarUDP` para activar la capacidad y `PuedeUsarUDP` para comprobar si está disponible.

El caso típico es información frecuente que se vuelve obsoleta rápidamente:

```text
Input
Posición aproximada
Rotación
Estado efímero
Telemetría frecuente
```

Si un paquete de posición de hace 100 ms llega después del de hace 20 ms, normalmente es mejor descartar el antiguo que retrasar toda la secuencia para conservarlo.

## No conviertas UDP en una copia de TCP [#no-conviertas-udp-en-una-copia-de-tcp]

Usar UDP no significa simplemente “mandar lo mismo por otro puerto”. El receptor debe tolerar pérdida, duplicación o ausencia de un mensaje concreto cuando la operación lo permita.

Para acciones que no pueden perderse, sigue utilizando el canal fiable.

## Nagle y latencia [#nagle-y-latencia]

`Eco.SinRetraso` controla el equivalente al ajuste `TCP_NODELAY`. Desactivando la agrupación de pequeños mensajes puedes reducir latencia, a costa de más paquetes y más overhead.

```csharp
Eco.SinRetraso = true;
```

No lo actives globalmente por reflejo. La decisión depende del patrón de tráfico del juego.

## UDP y estado de conexión [#udp-y-estado-de-conexión]

UDP no sustituye a la identidad de la sesión TCP. Eco puede utilizar el canal UDP como vía adicional asociada a la conexión principal.

```text
Sesión
├── TCP → control fiable
└── UDP → tráfico opcional de baja latencia
```

## Envíos directos [#envíos-directos]

Eco también admite enviar un `Buffer` a un `IPEndPoint` específico o difundirlo por LAN. Estos mecanismos son de bajo nivel y no deben confundirse con una RFC dirigida a un `Objeto`.

```text
Eco.EnviarPaquete(buffer, endpoint)
Eco.DifundirPaquete(buffer, puerto)
```

Úsalos cuando realmente necesites comunicación de transporte directa, por ejemplo descubrimiento LAN o una integración especial.

## Ping y diagnóstico [#ping-y-diagnóstico]

`Eco.Ping`/`Pinear` puede comprobar latencia sin convertir la medición en una operación de gameplay. El valor `Eco.Ping` representa el tiempo de ida y vuelta observado por la capa de red.

## Cómo elegir [#cómo-elegir]

```text
¿La operación puede perderse?
 ├── No → TCP
 │
 └── Sí
      │
      ├── ¿Se vuelve inútil rápidamente?
      │       ├── Sí → UDP
      │       └── No → TCP puede ser suficiente
      │
      └── ¿Requiere mucha frecuencia?
              └── Considera UDP + compresión / agrupación
```

<Callout title="Fiable no significa rápido" type="info">
  TCP es correcto para datos críticos, pero un diseño de red de tiempo real debe evitar depender de él para cada actualización de alta frecuencia. La fiabilidad puede crear head-of-line blocking cuando se acumulan mensajes.
</Callout>

<Card title="Paquetes" href="/docs/red/v1/transporte/paquetes">
  Cómo se construyen y envían paquetes.
</Card>

<Card title="Sincronización" href="/docs/red/v1/comunicacion/sincronizacion">
  Cómo modelar estado frecuente sin diseñar el transporte desde cero.
</Card>
