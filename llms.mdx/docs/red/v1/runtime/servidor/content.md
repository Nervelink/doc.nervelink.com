# Servidor (/docs/red/v1/runtime/servidor)



# Servidor [#servidor]

`ServidorJuego` mantiene el estado autoritativo de Eco. Gestiona conexiones, jugadores, canales, objetos dinámicos, paquetes, persistencia y servicios auxiliares.

<Callout title="Idea principal" type="info">
  El cliente solicita operaciones; el servidor valida y aplica esas operaciones sobre el estado compartido.
</Callout>

## Arquitectura [#arquitectura]

```text
ServidorJuego
├── Jugadores
├── Canales
├── TCP / UDP
├── Objetos / RFC
└── Persistencia
```

## Ciclo de vida [#ciclo-de-vida]

`Start()` prepara la configuración, listas administrativas, TCP y UDP opcional, activa el servidor e inicia el procesamiento de red. `Listen()` controla la escucha TCP.

## Jugadores [#jugadores]

El servidor mantiene estructuras para recorrer jugadores y para resolverlos rápidamente por identificador o endpoint.

## Canales [#canales]

Los canales activos se mantienen en listas y diccionarios para iteración y acceso rápido. Cada `Canal` contiene su estado, jugadores, objetos dinámicos y datos persistentes.

## Paquetes [#paquetes]

Los paquetes recibidos se procesan según el protocolo. Las operaciones que no corresponden al núcleo pueden dirigirse a `onCustomPacket` para extender Eco.

## Persistencia [#persistencia]

El servidor puede conservar datos de mundo mediante `Nodo` y funciones configurables de lectura/escritura. También puede poner canales sin jugadores en reposo mediante `Sleep()` y restaurarlos con `Wake()`.

## Administración y lobby [#administración-y-lobby]

`LoadAdminList()` y `SaveAdminList()` gestionan administradores. `enlaceLobbyLink` permite anunciar el servidor a un servicio de lobby.

## Multihilo [#multihilo]

Eco puede ejecutar el procesamiento en un hilo dedicado. Con `SINGLE_THREADED`, el procesamiento puede ejecutarse desde `Update()`.

## Servidor local [#servidor-local]

`localClient` permite conectar un `ClienteJuego` al servidor dentro del mismo proceso, utilizando colas internas en lugar de sockets.

## Referencia a TNet [#referencia-a-tnet]

| Eco                   | TNet         |
| --------------------- | ------------ |
| `ServidorJuego`       | `GameServer` |
| `Jugador` / `Entidad` | `Player`     |
| `Canal`               | `TNChannel`  |
| `Paquete`             | `TNPacket`   |

<Card title="Eco" href="https://github.com/Nervelink/eco">
  Código fuente de Eco.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Referencia externa para contrastar la arquitectura upstream.
</Card>
