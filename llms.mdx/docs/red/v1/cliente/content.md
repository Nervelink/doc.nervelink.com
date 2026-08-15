# Cliente (/docs/red/v1/cliente)



# Cliente [#cliente]

El cliente es la parte de Eco que mantiene la conexión con el servidor, procesa los paquetes entrantes, envía solicitudes y conserva el estado de la sesión local.

<Callout title="API frente a implementación" type="info">
  En el uso habitual de Eco se trabaja con su fachada pública. Internamente, el procesamiento de red está concentrado en `ClienteJuego` y en los protocolos de transporte que utiliza.
</Callout>

## Flujo general [#flujo-general]

```text
Juego
  │
  ▼
Eco
  │
  ▼
ClienteJuego
  │
  ├── Protocolo TCP
  ├── Protocolo UDP
  └── IConnection
       │
       ▼
    Servidor
```

`ClienteJuego` mantiene el protocolo TCP como transporte principal y puede habilitar UDP para tráfico apropiado para datagramas. También permite proporcionar una conexión personalizada mediante `IConnection`. fileciteturn80file0

## Estado de conexión [#estado-de-conexión]

El cliente expone varios estados que conviene distinguir:

| Estado              | Significado                                                       |
| ------------------- | ----------------------------------------------------------------- |
| `isConnected`       | Existe una conexión funcional con el servidor o un servidor local |
| `isTryingToConnect` | Se está intentando establecer la conexión                         |
| `isJoiningChannel`  | Hay una o más operaciones de entrada a canales pendientes         |
| `isInChannel`       | El cliente pertenece actualmente a uno o más canales              |
| `stage`             | Fase actual del protocolo TCP                                     |

La implementación mantiene además una colección explícita de canales y otra de canales pendientes de entrada. Esto es importante porque un cliente de Eco no se modela como miembro de un único canal. fileciteturn80file0

## Conexión TCP [#conexión-tcp]

TCP es el transporte principal de `ClienteJuego`.

```csharp
ClienteJuego cliente = ...;
cliente.Connect(endpoint);
```

La instancia mantiene un `ProtocoloTcp` interno y expone el protocolo mediante `protocol`. También puede asignarse una implementación de `IConnection` como transporte personalizado. fileciteturn80file0

La conexión puede establecerse contra un servidor remoto o contra un `ServidorJuego` local sin utilizar sockets. Este segundo modo es especialmente útil para pruebas, ejecución local y escenarios en los que el cliente y el servidor viven dentro del mismo proceso.

```csharp
cliente.Connect(servidorLocal);
```

## UDP [#udp]

UDP es opcional y se utiliza para tráfico que puede beneficiarse de menor latencia o de un modelo no fiable.

El cliente expone:

```text
StartUDP()
StopUDP()
canUseUDP
listeningPort
```

La implementación comprueba que exista un endpoint UDP del servidor y que el listener local esté activo antes de considerar que UDP puede utilizarse. fileciteturn80file0

El envío de paquetes distingue entre comunicaciones fiables y no fiables. Cuando el paquete requiere fiabilidad, `ClienteJuego` utiliza TCP; cuando no la requiere y UDP está disponible, puede enviarlo por UDP. fileciteturn26file0

## Procesamiento de paquetes [#procesamiento-de-paquetes]

El cliente procesa los paquetes entrantes en `ProcessPackets()`.

De forma simplificada:

```text
ProcessPackets
    │
    ├── recibe UDP
    │
    ├── procesa UDP
    │
    ├── recibe TCP
    │
    ├── procesa TCP
    │
    └── actualiza contadores / estado
```

Durante el procesamiento, Eco identifica el tipo mediante `Paquete` y delega el tratamiento correspondiente. El cliente mantiene también contadores de paquetes y bytes enviados y recibidos. fileciteturn39file0

El tiempo de procesamiento puede limitarse mediante la configuración de `USE_MAX_PACKET_TIME`, especialmente durante procesos como la entrada a un canal. Esto permite evitar que una gran cantidad de paquetes bloquee un frame completo. fileciteturn80file0

## Handshake y tiempo del servidor [#handshake-y-tiempo-del-servidor]

La conexión comienza con una fase de verificación del protocolo.

```text
Cliente
  │ RequestID
  ▼
Servidor
  │ ResponseID
  ▼
Cliente conectado
```

Cuando se recibe `ResponseID`, el cliente obtiene el identificador de jugador y sincroniza su reloj con el servidor. `serverTime` y `serverUptime` se calculan a partir de esa diferencia temporal. fileciteturn39file0

## Ping y supervisión [#ping-y-supervisión]

El cliente envía periódicamente solicitudes de ping al servidor. El ping sirve tanto para medir latencia como para mantener viva la conexión y confirmar que el servidor sigue siendo accesible.

La propiedad `ping` devuelve la latencia actual cuando existe una conexión. fileciteturn39file0

## Canales desde el cliente [#canales-desde-el-cliente]

El cliente conserva una lista de los canales en los que participa:

```csharp
cliente.channels
```

Y permite comprobar un canal concreto:

```csharp
cliente.IsInChannel(id);
cliente.IsJoiningChannel(id);
cliente.GetChannel(id);
```

También dispone de operaciones para:

```text
JoinChannel
LeaveChannel
LeaveAllChannels
CloseChannel
DeleteChannel
SetPlayerLimit
LoadLevel
SetHost
```

Estas operaciones generan paquetes de protocolo; el cliente no modifica unilateralmente el estado del servidor. fileciteturn27file0

## Protección durante la entrada a un canal [#protección-durante-la-entrada-a-un-canal]

`JoinChannel` añade el canal a una colección de entradas pendientes antes de que llegue la respuesta del servidor.

Esto tiene una finalidad importante: impedir que se envíen paquetes entre la orden de cambiar de escena o canal y la confirmación correspondiente. El código documenta explícitamente que esta protección evita que llamadas de la escena anterior terminen ejecutándose cuando la nueva escena ya está cargada. fileciteturn27file0

```text
JoinChannel()
    │
    ├── enviar solicitud
    ├── marcar canal como pendiente
    │
    ▼
Respuesta
    │
    └── finalizar entrada
```

## Jugador local [#jugador-local]

El jugador local del cliente está asociado al protocolo TCP y se expone mediante la información de jugador de `ClienteJuego`.

La implementación permite trabajar con:

```text
playerID
playerName
playerData
channels
serverTime
serverData
```

El cliente puede actualizar sus datos de jugador mediante `SetPlayerData()` y sincronizar explícitamente el árbol completo mediante `SyncPlayerData()`. fileciteturn80file0

## Datos del servidor [#datos-del-servidor]

El cliente conserva un `Nodo` con la configuración asociada al servidor conectado.

```csharp
cliente.serverData
```

La modificación de esos datos está restringida al estado administrativo del cliente; al cambiarse se genera una solicitud específica hacia el servidor. fileciteturn80file0

## Desconexión [#desconexión]

`Disconnect()` finaliza la conexión y limpia el estado de sesión asociado. En el modo de servidor local también detiene el servidor embebido.

Durante la desconexión se vacían los canales, jugadores conocidos, operaciones pendientes, callbacks y recursos UDP asociados a la sesión. fileciteturn26file0

## Cliente local sin sockets [#cliente-local-sin-sockets]

Eco permite ejecutar un `ServidorJuego` y un `ClienteJuego` dentro del mismo proceso.

```text
┌─────────────────────────────┐
│          Unity              │
│                             │
│  ClienteJuego ─── ServidorJuego
│       sin sockets            │
└─────────────────────────────┘
```

Este modo conserva el flujo lógico de cliente/servidor y resulta útil para pruebas y entornos locales. `ClienteJuego.Connect(ServidorJuego)` establece esta conexión local. fileciteturn26file0

## Referencia a TNet [#referencia-a-tnet]

La arquitectura de esta capa procede de `GameClient` de TNet 3 y conserva su separación entre cliente, TCP, UDP, canales y servidor local.

| Eco            | TNet                    |
| -------------- | ----------------------- |
| `ClienteJuego` | `GameClient`            |
| `ProtocoloTcp` | `TcpProtocol`           |
| `ProtocoloUdp` | `UdpProtocol`           |
| `Paquete`      | `Packet` / `TNPacket`   |
| `Canal`        | `Channel` / `TNChannel` |
| `Jugador`      | `Player`                |

La nomenclatura y las APIs documentadas aquí corresponden al repositorio `Nervelink/eco`.

## Referencias [#referencias]

<Card title="Eco" href="https://github.com/Nervelink/eco">
  Código fuente de la implementación actual.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream de la arquitectura heredada.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Referencia generada para explorar la arquitectura actual de TNet.
</Card>
