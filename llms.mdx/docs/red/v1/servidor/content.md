# Servidor (/docs/red/v1/servidor)



# Servidor [#servidor]

`ServidorJuego` es el núcleo que mantiene el estado autoritativo de Eco del lado servidor. Gestiona conexiones, jugadores, canales, objetos dinámicos, procesamiento de paquetes, persistencia y comunicación con servicios auxiliares como el lobby. fileciteturn82file0

<Callout title="Idea principal" type="info">
  El cliente solicita operaciones; el servidor mantiene el estado de la sesión y decide cómo se aplican, a qué canal pertenecen y qué jugadores deben recibir sus efectos.
</Callout>

## Arquitectura del servidor [#arquitectura-del-servidor]

```text
                    ServidorJuego
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
    Jugadores          Canales          Transporte
        │                 │                 │
    Entidad          Canal.cs       TCP / UDP
        │                 │
        └──────────┬──────┘
                   │
              Objetos / RFC
                   │
              Persistencia
```

`ServidorJuego` mantiene una lista secuencial de jugadores para recorrerlos y diccionarios para búsquedas rápidas por ID y por endpoint. También mantiene una lista y un diccionario de canales activos. fileciteturn82file0

## Ciclo de vida [#ciclo-de-vida]

El servidor se inicia mediante `Start`, que detiene una instancia previa, prepara la configuración, carga listas de ban y administración, configura TCP y opcionalmente UDP, activa el servidor e inicia el procesamiento de red. fileciteturn82file0

```csharp
var servidor = new ServidorJuego();
servidor.Start(5127, 5128);
```

El puerto TCP es la vía principal de conexión. UDP puede habilitarse adicionalmente para tráfico no fiable y comunicaciones frecuentes. fileciteturn82file0

## Escucha TCP [#escucha-tcp]

`Listen(int port)` crea el `TcpListener` del servidor. Eco mantiene una única escucha TCP activa y sustituye la anterior si se cambia el puerto. El estado se expone mediante `isListening` y `tcpPort`. fileciteturn82file0

```text
Listen(port)
    ↓
TcpListener
    ↓
Aceptación de conexiones
    ↓
Entidad / Jugador
```

## UDP [#udp]

UDP es opcional. `Start` puede abrir un puerto UDP independiente y Eco expone el estado mediante `udpPort`. En el lado servidor se utiliza para el transporte que no necesita las garantías de TCP. fileciteturn82file0

No debe asumirse que todo mensaje utiliza UDP: el protocolo decide cuándo una transmisión puede ser no fiable y cuándo debe permanecer sobre TCP.

## Jugadores [#jugadores]

El servidor representa las conexiones autenticadas mediante `Entidad`, mientras que `Jugador` proporciona la información común del participante. El servidor mantiene:

```text
mPlayerLista
mPlayerDict
mDictionaryEP
```

La primera estructura permite iteración; las otras aceleran las búsquedas por identificador y endpoint. fileciteturn82file0

El número de participantes puede consultarse mediante `playerCount` y la colección mediante `players`. fileciteturn82file0

## Canales [#canales]

El servidor mantiene todos los canales activos en `mChannelLista` y `mChannelDict`. Cada `Canal` contiene jugadores, objetos creados, RFC persistentes, objetos destruidos y sus datos asociados. fileciteturn82file0 fileciteturn62file0

```text
ServidorJuego
├── Canal 1
│   ├── Jugadores
│   ├── Objetos
│   └── RFC persistentes
│
├── Canal 2
│   └── ...
└── Canal N
```

Esto es la base del modelo multi-canal de Eco: los canales son estados independientes dentro del mismo servidor.

## Persistencia [#persistencia]

El servidor mantiene datos asociados al mundo mediante `Nodo` y utiliza funciones configurables de lectura y escritura para cargar y guardar esos datos. El archivo por defecto del estado del servidor es `world.dat`, aunque la implementación permite sustituir los callbacks de almacenamiento. fileciteturn82file0

También existe el concepto de `Sleep`/`Wake` para reducir el uso de memoria de canales sin jugadores. El servidor puede poner los canales inactivos en reposo y restaurarlos posteriormente. fileciteturn82file0

## Conexión local sin sockets [#conexión-local-sin-sockets]

Dentro de Unity, Eco puede conectar un `ClienteJuego` directamente a un `ServidorJuego` mediante `localClient`. En este modo se intercambian colas internas en lugar de utilizar sockets de red. fileciteturn82file0

```text
ClienteJuego
    │
    │ Queue<Buffer>
    ▼
ServidorJuego
```

Esto es útil para ejecutar un servidor local, pruebas y escenarios donde no se desea depender de una conexión TCP real.

## Administración [#administración]

`ServidorJuego` mantiene una lista de administradores, cargada y guardada mediante `LoadAdminList` y `SaveAdminList`. La ruta por defecto es `ServerConfig/admin.txt`. fileciteturn82file0

El servidor también dispone de `gameID`, un identificador utilizado para distinguir la instancia de juego al integrarse con un lobby. fileciteturn82file0

## Lobby [#lobby]

La propiedad `enlaceLobbyLink` permite asociar el servidor a un servicio de lobby. Al iniciar o detener la escucha, el servidor puede iniciar o detener dicho enlace y enviar una actualización de su estado. fileciteturn82file0

El lobby no sustituye al servidor de juego: sirve para anunciar y descubrir servidores.

## Procesamiento de paquetes [#procesamiento-de-paquetes]

El servidor recibe paquetes desde TCP y UDP, los interpreta y ejecuta la operación correspondiente. Los paquetes que no son manejados por la lógica interna pueden llegar a `onCustomPacket`, permitiendo extender el protocolo sin modificar el núcleo. fileciteturn82file0

```text
Red
 ↓
Paquete
 ↓
ServidorJuego
 ├── protocolo interno
 └── onCustomPacket
```

## Eventos del servidor [#eventos-del-servidor]

La API expone callbacks para integrar la lógica del juego:

| Evento               | Uso                   |
| -------------------- | --------------------- |
| `onPlayerConnect`    | Jugador autenticado   |
| `onPlayerDisconnect` | Jugador desconectado  |
| `onShutdown`         | Apagado del servidor  |
| `onReceivePackets`   | Recepción de paquetes |
| `onCustomPacket`     | Paquetes propios      |

Estas extensiones permiten integrar la lógica específica del juego sin acoplarla al procesamiento base del protocolo. fileciteturn82file0

## Servidor multihilo [#servidor-multihilo]

Eco puede ejecutar el procesamiento del servidor en un hilo independiente. `ServidorJuego` expone `isMultiThreaded` y, cuando el modo multihilo está activo, inicia un hilo dedicado al procesamiento. Con `SINGLE_THREADED` definido, `Update()` puede ejecutar el procesamiento desde el hilo principal. fileciteturn82file0

```text
Multihilo
ServidorJuego
    ↓
Thread de red

Monohilo
ServidorJuego
    ↓
Update()
```

La elección afecta especialmente a cómo se integra el servidor con Unity y a qué código puede ejecutarse directamente desde callbacks de red.

## Relación con el cliente [#relación-con-el-cliente]

El cliente mantiene la conexión y las peticiones; el servidor mantiene el estado autoritativo:

```text
ClienteJuego
   │
   │ RequestJoinChannel
   │ RequestCreateObject
   │ RFC / datos
   ▼
ServidorJuego
   │
   ├── valida
   ├── modifica estado
   ├── actualiza Canal
   └── responde / reenvía
        │
        ▼
   otros clientes
```

Este modelo es especialmente importante para entender propiedad, persistencia y transferencia de objetos.

## Referencia a TNet [#referencia-a-tnet]

`ServidorJuego` conserva la arquitectura de `GameServer` de TNet. Eco ha adaptado nombres y tipos, pero el modelo de servidor sigue siendo esencialmente el mismo en esta capa. fileciteturn82file0

| TNet         | Eco                   |
| ------------ | --------------------- |
| `GameServer` | `ServidorJuego`       |
| `Player`     | `Jugador` / `Entidad` |
| `TNChannel`  | `Canal`               |
| `TNBuffer`   | `Buffer`              |
| `TNPacket`   | `Paquete`             |

## Referencias [#referencias]

<Card title="Eco" href="https://github.com/Nervelink/eco">
  Código fuente de la implementación de Eco.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream de la arquitectura en la que se basa Eco.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Documentación generada para explorar la arquitectura actual de TNet upstream.
</Card>
