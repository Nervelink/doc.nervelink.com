# TCP y UDP (/docs/red/v1/transporte/tcp-udp)



# TCP y UDP [#tcp-y-udp]

Eco dispone de un transporte principal basado en TCP y de una ruta UDP para tráfico que puede beneficiarse de menor latencia o tolerar pérdida de paquetes.

```text
Eco
 │
 ├── TCP → fiable / ordenado
 │
 └── UDP → rápido / no fiable
```

## TCP [#tcp]

TCP es la vía principal de comunicación del cliente con el servidor. Se utiliza para operaciones que necesitan entrega fiable y ordenada.

El cliente mantiene `ProtocoloTcp` como su protocolo principal y expone el estado de conexión a través de `ClienteJuego`.

## UDP [#udp]

Cuando está disponible, `ProtocoloUdp` permite enviar tráfico que no necesita las garantías completas de TCP.

El cliente puede comprobar `canUseUDP` y conocer el puerto de escucha mediante `listeningPort`.

## Activación [#activación]

UDP no se considera disponible simplemente porque exista una instancia del protocolo. El servidor debe proporcionar un puerto UDP y el cliente debe completar su activación.

```text
Conexión TCP
     ↓
negociación UDP
     ↓
activación
     ↓
tráfico UDP disponible
```

## Qué transporte elegir [#qué-transporte-elegir]

La elección depende de la semántica del mensaje:

| Característica             | TCP                           | UDP                                    |
| -------------------------- | ----------------------------- | -------------------------------------- |
| Entrega fiable             | Sí                            | No                                     |
| Orden de llegada           | Sí                            | No garantizado                         |
| Retransmisión              | Sí                            | No                                     |
| Latencia potencial         | Mayor bajo pérdida/congestión | Menor para tráfico tolerante a pérdida |
| Estado crítico             | Adecuado                      | No suele ser adecuado                  |
| Actualizaciones frecuentes | Puede ser adecuado            | Suele ser más apropiado                |

No se debe elegir UDP simplemente por ser más rápido: si el dato necesita entrega garantizada, hay que utilizar un mecanismo fiable.

## `Send` y `SendQuickly` [#send-y-sendquickly]

Eco abstrae gran parte de esta decisión detrás de sus operaciones de envío. `Send` representa el camino normal, mientras que `SendQuickly` permite utilizar el camino optimizado para datos donde la fiabilidad completa no sea necesaria.

La capa de gameplay no debería construir datagramas manualmente salvo que esté implementando una extensión de transporte.

## Nagle y latencia TCP [#nagle-y-latencia-tcp]

`ClienteJuego.noDelay` permite activar o desactivar el algoritmo de Nagle. Activarlo puede reducir la latencia de pequeños mensajes a costa de aumentar el tráfico.

```csharp
cliente.noDelay = true;
```

La misma configuración se comunica al servidor para mantener el comportamiento coherente del socket TCP.

## Servidor [#servidor]

`ServidorJuego` puede abrir un puerto TCP y, opcionalmente, un puerto UDP al arrancar.

```csharp
servidor.Start(tcpPort: 5127, udpPort: 5128);
```

El puerto UDP sólo se activa cuando se proporciona uno válido durante el arranque.

## Restricciones de plataforma [#restricciones-de-plataforma]

La disponibilidad de UDP depende de la plataforma y de las compilaciones de Eco. Algunas configuraciones, como determinados entornos antiguos de Unity o modos de modificación, deshabilitan explícitamente UDP.

Por eso la aplicación debe consultar la disponibilidad real (`canUseUDP`) en lugar de asumir que UDP siempre está disponible.

## Relación con Paquete [#relación-con-paquete]

El transporte no cambia el significado de un `Paquete`.

```text
Paquete
  ↓
Buffer
  ↓
TCP o UDP
  ↓
Buffer
  ↓
Paquete
```

El mismo concepto de protocolo puede utilizar distintas rutas de transporte según el tipo de mensaje.

## Relación con TNet [#relación-con-tnet]

El modelo corresponde al de los protocolos TCP y UDP de TNet, pero Eco utiliza `ProtocoloTcp`, `ProtocoloUdp`, `ClienteJuego` y `ServidorJuego` como API interna.

<Card title="Eco · ClienteJuego" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/ClienteJuego.cs">
  Gestión cliente de TCP, UDP y envío de paquetes.
</Card>

<Card title="Eco · ServidorJuego" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Servidor/ServidorJuego.cs">
  Gestión servidor de listeners TCP/UDP.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream.
</Card>
