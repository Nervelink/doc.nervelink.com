# Protocolo de paquetes (/docs/red/v1/referencia/api/protocolo-paquetes)



# Protocolo de paquetes [#protocolo-de-paquetes]

`Paquete` es un catálogo de operaciones de protocolo. La intención de esta página no es obligar a memorizar cada valor del enum, sino explicar qué familia representa cada mensaje y qué flujo implementa.

<Callout title="API de infraestructura" type="warn">
  La mayoría de estos paquetes son utilizados internamente por `Eco`, `ClienteJuego` y `ServidorJuego`. Para gameplay utiliza primero `Eco`, `Objeto`, `Componente` y RFC.
</Callout>

## Conexión y handshake [#conexión-y-handshake]

Los mensajes iniciales establecen identidad y compatibilidad:

| Paquete        | Función                                                                  |
| -------------- | ------------------------------------------------------------------------ |
| `RequestID`    | Primer mensaje del cliente. Envía versión, nombre y datos del jugador.   |
| `ResponseID`   | Confirma protocolo, asigna `playerID` y proporciona tiempo del servidor. |
| `Disconnect`   | Solicita o indica el cierre de la conexión.                              |
| `Error`        | Comunica un error de protocolo o sesión.                                 |
| `RequestPing`  | Solicita una medida de latencia/tiempo.                                  |
| `ResponsePing` | Devuelve información temporal y estado agregado.                         |

```text
Cliente                 Servidor
   │ RequestID              │
   ├───────────────────────>│
   │<───────────────────────┤ ResponseID
   │                        │
   │        sesión lista    │
```

## Activación de UDP [#activación-de-udp]

La conexión TCP puede negociar posteriormente la ruta UDP:

```text
RequestSetUDP
      ↓
ResponseSetUDP
      ↓
RequestActivateUDP
      ↓
UDP activo
```

`RequestActivateUDP` se envía por UDP y no necesita una respuesta convencional.

## Canales [#canales]

Las operaciones de canal incluyen:

```text
RequestJoinChannel
ResponseJoiningChannel
ResponseJoinChannel
ResponsePlayerJoined
RequestLeaveChannel
ResponseLeaveChannel
ResponsePlayerLeft
RequestCloseChannel
RequestSetPlayerLimit
RequestLoadLevel
ResponseLoadLevel
RequestChannelList
ResponseChannelList
```

El flujo de entrada es deliberadamente más largo que una simple petición porque el servidor debe reconstruir jugadores, objetos y estado del canal antes de confirmar la incorporación.

## Jugadores [#jugadores]

Los paquetes relacionados con jugadores transportan identidad y datos:

```text
RequestSetName
ResponseRenamePlayer
RequestSetPlayerData
ResponseSetPlayerData
ResponsePlayerJoined
ResponsePlayerLeft
```

El `id` de sesión y los datos del jugador viajan separados del estado de los objetos del mundo.

## Objetos [#objetos]

El ciclo de vida de un objeto utiliza:

```text
RequestCreateObject
ResponseCreateObject
RequestTransferObject
ResponseTransferObject
RequestDestroyObject
ResponseDestroyObject
```

La creación incluye canal, persistencia, método de creación, prefab y parámetros serializados. La transferencia contiene canal antiguo, canal nuevo, ID antiguo y nuevo.

<Callout title="Transferencia" type="info">
  La transferencia no es equivalente a mover un `GameObject` local. Es una operación de protocolo que modifica el contexto de red del objeto y puede producir una nueva identidad dentro del canal destino.
</Callout>

## Reenvío de RFC [#reenvío-de-rfc]

Las operaciones de alto nivel se transportan mediante familias de forwarding:

| Paquete                | Comportamiento                                                       |
| ---------------------- | -------------------------------------------------------------------- |
| `ForwardToAll`         | Todos los participantes actuales.                                    |
| `ForwardToAllSaved`    | Todos los actuales y futuros participantes según el estado guardado. |
| `ForwardToOthers`      | Todos excepto el emisor.                                             |
| variantes persistentes | Mantienen la operación para reconstrucción posterior.                |

El protocolo conserva la información necesaria para localizar objeto, RFC y canal antes de entregar los parámetros al runtime.

## Archivos [#archivos]

La capa de archivos dispone de mensajes para:

```text
RequestGetFileList
ResponseGetFileList
RequestSaveFile
RequestLoadFile
ResponseLoadFile
RequestDeleteFile
```

Estos mensajes forman el servicio de archivos del servidor y no deben confundirse con la persistencia automática de objetos y canales.

## Datos de canal [#datos-de-canal]

El protocolo dispone de mensajes específicos para modificar y notificar cambios de `DatosCanal`:

```text
RequestSetChannelData
ResponseSetChannelData
```

La ruta se transmite junto al canal y al valor serializado.

## Configuración TCP [#configuración-tcp]

`RequestNoDelay` permite cambiar la opción de Nagle en la conexión establecida.

## Echo y callbacks [#echo-y-callbacks]

Las versiones modernas de la arquitectura también incluyen un paquete de retorno/eco para confirmar recorridos de red y ejecutar callbacks cuando una operación alcanza de nuevo el cliente.

Esto es la base de funcionalidades como `EnviarConRetorno`, `EsperarRetorno` y pruebas de latencia sin implementar un protocolo paralelo.

## Diagnóstico [#diagnóstico]

Cuando se inspecciona un paquete, registra como mínimo:

```text
Paquete
Canal
Jugador origen
Objeto / RFC
Longitud
Transporte
Fiabilidad
```

Esa información permite distinguir un error de gameplay de un error de protocolo.

## Fuentes [#fuentes]

<Card title="Paquete.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Comun/Paquete.cs">
  Catálogo real de paquetes de Eco.
</Card>

<Card title="ClienteJuego" href="/docs/red/v1/referencia/api/cliente-juego">
  Procesamiento desde el cliente.
</Card>

<Card title="ServidorJuego" href="/docs/red/v1/referencia/api/servidor-juego">
  Procesamiento desde el servidor.
</Card>
