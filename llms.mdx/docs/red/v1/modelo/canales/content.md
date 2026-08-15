# Canales (/docs/red/v1/modelo/canales)



# Canales [#canales]

Un `Canal` define un ámbito de estado compartido dentro del servidor de Eco. Los jugadores que forman parte de un canal pueden recibir la información que ese canal distribuye, incluyendo mensajes y estado asociado a sus objetos.

<Callout title="Punto importante" type="info">
  Una conexión no está limitada a un único canal. Eco mantiene una colección de canales activos por cliente, por lo que un mismo jugador puede participar simultáneamente en varios ámbitos de red.
</Callout>

## Modelo [#modelo]

```text
Servidor
│
├── Canal 10
│   ├── Jugador A
│   └── Jugador B
│
├── Canal 20
│   ├── Jugador B
│   └── Jugador C
│
└── Canal 30
    └── Jugador A
```

El mismo jugador puede aparecer en varios canales. En el cliente, esta pertenencia se representa mediante una colección interna de `Canal` mantenida por `ClienteJuego`. La API expone esa colección mediante `channels`.

## Estado de un canal [#estado-de-un-canal]

`Canal` mantiene información suficiente para representar tanto su configuración como su estado de red. Entre sus propiedades se encuentran `id`, `password`, `level`, `isPersistent`, `isClosed`, `isLeaving`, `playerLimit`, `jugadores`, `rfcs`, `created`, `destroyed` y `host`.

## Unirse a un canal [#unirse-a-un-canal]

El cliente utiliza `ClienteJuego.JoinChannel` para solicitar la entrada en un canal. La solicitud incluye identificador, contraseña, nivel, persistencia y límite de jugadores.

```csharp
cliente.JoinChannel(
    channelID: 10,
    levelName: "Arena",
    persistent: false,
    playerLimit: 4,
    password: ""
);
```

Durante el proceso de unión, Eco registra el canal como pendiente hasta recibir la respuesta del servidor.

## Múltiples canales simultáneos [#múltiples-canales-simultáneos]

La pertenencia a canales es acumulativa. Un cliente puede participar simultáneamente en varios canales:

```csharp
cliente.JoinChannel(10, "Mundo", true, 100, "");
cliente.JoinChannel(20, "Partida", false, 4, "");
```

`IsInChannel(id)` comprueba la pertenencia y `GetChannel(id)` obtiene la instancia local.

<Callout title="No confundir canal y transporte" type="warn">
  Dos canales distintos no implican dos conexiones de socket distintas. La conexión y el transporte pertenecen a una capa inferior; el canal delimita el estado y los destinatarios.
</Callout>

## Consultar y abandonar [#consultar-y-abandonar]

```csharp
bool cualquiera = cliente.isInChannel;
bool estaDentro = cliente.IsInChannel(20);
bool uniendose = cliente.IsJoiningChannel(20);
Canal canal = cliente.GetChannel(20);

cliente.LeaveChannel(20);
cliente.LeaveAllChannels();
```

Salir de un canal no implica desconectarse del servidor.

## Persistencia y propiedad [#persistencia-y-propiedad]

Un canal puede ser persistente. Cuando el último jugador abandona, Eco puede destruir objetos no persistentes, transferir la propiedad de otros objetos o cerrar y reiniciar el canal según su configuración.

También mantiene objetos dinámicos y RFC asociadas para poder reconstruir el estado cuando corresponde.

## Nivel y host [#nivel-y-host]

Cada canal puede tener un `level` y un `host`. El cambio de nivel se coordina a través del sistema de red y el host puede cambiar cuando abandona el canal.

## Correspondencia con TNet [#correspondencia-con-tnet]

| TNet                     | Eco                         |
| ------------------------ | --------------------------- |
| `TNChannel` / `Channel`  | `Canal`                     |
| `TNManager.JoinChannel`  | `ClienteJuego.JoinChannel`  |
| `TNManager.LeaveChannel` | `ClienteJuego.LeaveChannel` |
| `TNManager.IsInChannel`  | `ClienteJuego.IsInChannel`  |
| `TNManager.GetChannel`   | `ClienteJuego.GetChannel`   |

## Referencias [#referencias]

<Card title="Eco" href="https://github.com/Nervelink/eco/tree/main/src/Assets/Pandora/Logica/Nucleo/Core/Red">
  Código fuente de la implementación de canales.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Referencia generada sobre la implementación actual de TNet.
</Card>
