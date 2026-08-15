# Canales (/docs/red/v1/canales)



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

El mismo jugador puede aparecer en varios canales. En el cliente, esta pertenencia se representa mediante una colección interna de `Canal` mantenida por `ClienteJuego`. La API expone esa colección mediante `channels`. citeeco-client-game

## Estado de un canal [#estado-de-un-canal]

`Canal` mantiene información suficiente para representar tanto su configuración como su estado de red. Entre sus propiedades se encuentran:

| Propiedad      | Función                                                         |
| -------------- | --------------------------------------------------------------- |
| `id`           | Identificador del canal.                                        |
| `password`     | Contraseña del canal.                                           |
| `level`        | Nivel asociado al canal.                                        |
| `isPersistent` | Indica si el canal debe conservarse cuando queda sin jugadores. |
| `isClosed`     | Impide nuevas incorporaciones al canal.                         |
| `isLeaving`    | Indica que el cliente está saliendo del canal.                  |
| `playerLimit`  | Límite máximo de jugadores.                                     |
| `jugadores`    | Jugadores presentes en el canal.                                |
| `rfcs`         | Llamadas remotas almacenadas para el estado del canal.          |
| `created`      | Objetos dinámicos creados dentro del canal.                     |
| `destroyed`    | Identificadores de objetos destruidos.                          |
| `host`         | Jugador que actúa como host del canal.                          |

La implementación también mantiene estructuras internas para localizar objetos dinámicos rápidamente y para cargar datos del canal de forma diferida. citeeco-canal

## Unirse a un canal [#unirse-a-un-canal]

El cliente utiliza `ClienteJuego.JoinChannel` para solicitar la entrada en un canal. La solicitud incluye:

* identificador del canal;
* contraseña, si existe;
* nivel que debe cargarse;
* si el canal es persistente;
* límite de jugadores.

El método no permite solicitar de nuevo un canal en el que el cliente ya está presente. Una vez enviada la solicitud, Eco registra el canal como pendiente de unión hasta recibir la respuesta del servidor. citeeco-join-channel

```csharp
cliente.JoinChannel(
    channelID: 10,
    levelName: "Arena",
    persistent: false,
    playerLimit: 4,
    password: ""
);
```

Durante el proceso de unión, Eco bloquea temporalmente el envío de determinados paquetes para evitar que mensajes pertenecientes a la escena anterior se procesen después de solicitar el cambio de nivel. El propio código documenta esta protección como parte del flujo de `JoinChannel`. citeeco-join-channel

## Múltiples canales simultáneos [#múltiples-canales-simultáneos]

La pertenencia a canales es acumulativa. Un cliente puede unirse a un segundo canal sin abandonar el primero:

```csharp
cliente.JoinChannel(10, "Mundo", true, 100, "");
cliente.JoinChannel(20, "Partida", false, 4, "");
```

Después de completar ambas operaciones, `ClienteJuego.channels` contiene los dos canales. `IsInChannel(id)` permite comprobar la pertenencia a uno concreto, mientras que `GetChannel(id)` obtiene la instancia local del canal. citeeco-cliente-canales

Esto permite utilizar los canales como ámbitos independientes. Un diseño de juego podría, por ejemplo, utilizar un canal persistente para un mundo y otros canales temporales para partidas, eventos o actividades aisladas.

<Callout title="No confundir canal y transporte" type="warn">
  Dos canales distintos no implican dos conexiones de socket distintas. La conexión y el transporte se gestionan en una capa inferior; los canales delimitan el estado y los destinatarios dentro de esa conexión.
</Callout>

## Consultar canales [#consultar-canales]

Eco proporciona varias operaciones para inspeccionar la pertenencia del cliente:

```csharp
// ¿Estoy en algún canal?
bool cualquiera = cliente.isInChannel;

// ¿Estoy en un canal concreto?
bool estaDentro = cliente.IsInChannel(20);

// ¿Estoy intentando entrar?
bool uniendose = cliente.IsJoiningChannel(20);

// Obtener el canal local.
Canal canal = cliente.GetChannel(20);
```

`isJoiningChannel` representa cualquier unión pendiente, mientras que `IsJoiningChannel(id)` permite consultar una unión concreta. citeeco-cliente-canales

## Salir de un canal [#salir-de-un-canal]

Para abandonar un canal concreto se utiliza `LeaveChannel(id)`. La operación marca primero el canal como `isLeaving` para evitar solicitudes duplicadas y envía la petición al servidor. citeeco-leave-channel

```csharp
cliente.LeaveChannel(20);
```

También existe `LeaveAllChannels()`, que recorre los canales actuales y solicita la salida de cada uno de ellos. citeeco-leave-all

Salir de un canal no equivale a desconectarse del servidor. El cliente puede permanecer conectado mientras abandona uno o varios canales y continúa participando en otros.

## Persistencia [#persistencia]

Un canal puede marcarse como persistente mediante `isPersistent`. Esta propiedad afecta a su ciclo de vida cuando queda sin jugadores.

En la implementación de `Canal`, cuando el último jugador abandona:

* los objetos no persistentes asociados a ese jugador pueden destruirse;
* otros objetos pueden transferir su propietario a otro jugador;
* un canal no persistente puede cerrarse y reiniciarse cuando queda vacío.

El comportamiento concreto está implementado en `Canal.RemovePlayer`. citeeco-canal-remove

En otras palabras, la persistencia del canal y la persistencia de un objeto son conceptos relacionados, pero no equivalentes.

## Límite de jugadores [#límite-de-jugadores]

Cada canal tiene un `playerLimit`. El valor por defecto de la implementación es `65535` y `JoinChannel` normaliza cualquier valor superior a ese máximo antes de enviar la solicitud. citeeco-canal citeeco-join-channel

El límite puede modificarse posteriormente con:

```csharp
cliente.SetPlayerLimit(channelID, max);
```

La operación sólo se envía cuando el cliente está conectado y pertenece al canal indicado. citeeco-set-player-limit

## Cierre y eliminación [#cierre-y-eliminación]

Cerrar un canal evita que nuevos jugadores puedan incorporarse y es una operación irreversible para ese canal. La API expone `CloseChannel(channelID)` para solicitar este cambio. citeeco-close-channel

Eliminar un canal es una operación diferente y se solicita mediante `DeleteChannel(id, disconnect)`. El segundo parámetro permite indicar si la operación debe producir además una desconexión. citeeco-delete-channel

## Nivel asociado al canal [#nivel-asociado-al-canal]

Cada canal puede tener asociado un `level`. Eco permite cambiar el nivel de un canal mediante:

```csharp
cliente.LoadLevel(channelID, "Arena");
```

La operación se restringe a canales en los que el cliente ya está presente. citeeco-load-level

Esto es importante en juegos donde el cambio de escena forma parte del estado compartido del canal: la carga del nivel no es simplemente una operación local de Unity, sino una operación coordinada por la infraestructura de red.

## Host del canal [#host-del-canal]

Cada canal mantiene un `host`. En el cliente, `GetHost(channelID)` devuelve el jugador que actualmente actúa como host del canal, siempre que el cliente pertenezca a ese canal. citeeco-host

La implementación del canal también contempla qué ocurre cuando el host abandona: primero se limpia la referencia y el servidor puede reasignar el papel dentro del flujo de salida. citeeco-canal-remove

## Objetos dentro del canal [#objetos-dentro-del-canal]

Los canales almacenan información sobre los objetos dinámicos creados dentro de ellos. `Canal` mantiene:

```text
Canal
├── rfcs
├── created
├── destroyed
└── datos del canal
```

Los objetos creados dinámicamente tienen identificadores únicos y el canal conserva su información de creación para poder exportarlos, importarlos o reconstruir su estado cuando corresponde. citeeco-canal-objects

Los identificadores dinámicos comienzan por encima de `32767`; el rango inferior está reservado para objetos existentes en la escena. citeeco-canal

## Propiedad de objetos al salir [#propiedad-de-objetos-al-salir]

Cuando un jugador abandona un canal, Eco revisa los objetos dinámicos creados por ese jugador.

Los objetos marcados para destruirse al abandonar se eliminan y sus RFC asociados se limpian. Los objetos que deben sobrevivir pueden transferir su propietario a otro jugador del canal. citeeco-canal-remove

Este mecanismo hace que la salida de un jugador sea también una operación de mantenimiento del estado del canal.

## Flujo recomendado [#flujo-recomendado]

```text
Conectar al servidor
        ↓
Solicitar Canal A
        ↓
Respuesta de unión
        ↓
Participar en Canal A
        │
        ├── Solicitar Canal B
        │       ↓
        │   Participar también en B
        │
        └── Salir de A
                ↓
          Continuar en B
                ↓
         Desconectar del servidor
```

La ventaja del modelo es que la vida del cliente, la conexión y la pertenencia a canales son estados independientes.

## Correspondencia con TNet [#correspondencia-con-tnet]

Eco mantiene prácticamente el mismo modelo de canales de TNet, pero utiliza sus propios nombres:

| TNet                         | Eco                             |
| ---------------------------- | ------------------------------- |
| `TNChannel` / `Channel`      | `Canal`                         |
| `TNManager.JoinChannel`      | `ClienteJuego.JoinChannel`      |
| `TNManager.LeaveChannel`     | `ClienteJuego.LeaveChannel`     |
| `TNManager.LeaveAllChannels` | `ClienteJuego.LeaveAllChannels` |
| `TNManager.IsInChannel`      | `ClienteJuego.IsInChannel`      |
| `TNManager.GetChannel`       | `ClienteJuego.GetChannel`       |

La implementación documentada aquí corresponde al código de `eco`, no a la API histórica de TNet.

## Referencias upstream [#referencias-upstream]

<Card title="Canales en Eco" href="https://github.com/Nervelink/eco/tree/main/src/Assets/Pandora/Logica/Nucleo/Core/Red">
  Código fuente de `Canal` y gestión de canales en Eco.
</Card>

<Card title="TNet en GitHub" href="https://github.com/tasharen/tnet">
  Repositorio upstream del que procede esta arquitectura.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Documentación generada sobre el repositorio actual de TNet para contrastar conceptos y evolución.
</Card>
