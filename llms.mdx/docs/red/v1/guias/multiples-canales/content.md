# Varios canales simultáneos (/docs/red/v1/guias/multiples-canales)



# Varios canales simultáneos [#varios-canales-simultáneos]

Una de las características importantes del modelo de Eco es que un cliente puede pertenecer a varios canales al mismo tiempo. La pertenencia a un canal no sustituye a la anterior: cada `Canal` mantiene su propio contexto de red dentro de la misma conexión.

<Callout title="Idea principal" type="info">
  Un cliente puede estar conectado una sola vez al servidor y participar simultáneamente en varios canales. Cada canal representa un ámbito independiente de estado compartido.
</Callout>

## Escenario [#escenario]

Un caso práctico puede ser un juego que mantiene:

```text
Conexión
│
├── Canal 10 · Mundo
│   └── Estado persistente del mundo
│
├── Canal 20 · Partida
│   └── Estado de la partida actual
│
└── Canal 30 · Evento
    └── Actividad temporal
```

El jugador puede permanecer en `Mundo` mientras entra y sale de `Partida` y `Evento`.

## Entrar en varios canales [#entrar-en-varios-canales]

El cliente puede solicitar sucesivas entradas sin abandonar el canal anterior:

```csharp
cliente.JoinChannel(10, "Mundo", true, 100, "");
cliente.JoinChannel(20, "Partida", false, 4, "");
cliente.JoinChannel(30, "Evento", false, 32, "");
```

Las solicitudes de entrada quedan registradas como pendientes hasta que Eco recibe la confirmación correspondiente. Esto permite que el estado local diferencie entre un canal ya activo y uno que todavía está completando su incorporación.

## Consultar la pertenencia [#consultar-la-pertenencia]

```csharp
bool enMundo = cliente.IsInChannel(10);
bool enPartida = cliente.IsInChannel(20);
bool enEvento = cliente.IsInChannel(30);

Canal mundo = cliente.GetChannel(10);
Canal partida = cliente.GetChannel(20);
```

También puede comprobarse si una entrada concreta sigue pendiente:

```csharp
bool uniendose = cliente.IsJoiningChannel(20);
```

## Los canales no son sockets [#los-canales-no-son-sockets]

La presencia en varios canales no implica crear varias conexiones TCP o UDP.

```text
                 Una conexión
                      │
          ┌───────────┼───────────┐
          │           │           │
       Canal 10     Canal 20    Canal 30
```

El transporte sigue perteneciendo a la conexión. El canal determina el contexto de estado y los destinatarios que corresponden a esa parte de la sesión.

<Callout title="Diseño recomendado" type="warn">
  No mantengas una conexión por canal salvo que exista una necesidad específica fuera del modelo de Eco. Normalmente los canales deben utilizarse como ámbitos lógicos dentro de una misma conexión.
</Callout>

## Objetos y canales [#objetos-y-canales]

Los objetos de red tienen un `channelID` y su identidad completa utiliza también el contexto del canal. Por eso dos objetos con el mismo identificador local pueden pertenecer a canales diferentes sin representar la misma entidad de red.

```text
Canal 10
└── Objeto 100

Canal 20
└── Objeto 100
```

Para operaciones de red, el canal forma parte del contexto necesario para localizar correctamente el objeto.

## Salir de un canal [#salir-de-un-canal]

Abandonar un canal no desconecta al cliente del servidor ni afecta automáticamente a los demás canales:

```csharp
cliente.LeaveChannel(20);
```

Después de la salida:

```text
Conexión
│
├── Canal 10 ✅
├── Canal 20 ❌
└── Canal 30 ✅
```

También existe `LeaveAllChannels()` para solicitar la salida de todos los canales activos.

## Cambiar el nivel de un canal [#cambiar-el-nivel-de-un-canal]

El nivel asociado a un canal es parte de su estado. Eco permite solicitar el cambio de nivel para un canal concreto:

```csharp
cliente.LoadLevel(20, "Arena");
```

Esto debe entenderse como una operación coordinada del canal y no simplemente como una llamada local a `SceneManager.LoadScene`.

El cambio de nivel forma parte además del motivo por el que Eco mantiene protecciones durante la entrada a canales: mientras la transición está en curso, evita que determinadas operaciones de la escena anterior se ejecuten fuera de contexto.

## Ejemplo: mundo + partida [#ejemplo-mundo--partida]

Un patrón habitual es mantener un canal persistente para el mundo y otro temporal para una partida:

```csharp
// El jugador permanece en el mundo.
cliente.JoinChannel(10, "Mundo", true, 100, "");

// Se crea una sesión temporal de partida.
cliente.JoinChannel(20, "Partida", false, 4, "");
```

La lógica puede tratar cada canal de forma independiente:

```csharp
if (cliente.IsInChannel(20))
{
    // Estado de la partida.
}

if (cliente.IsInChannel(10))
{
    // Estado persistente del mundo.
}
```

Esto resulta especialmente útil cuando diferentes partes del juego necesitan ciclos de vida distintos.

## Cuándo utilizar canales [#cuándo-utilizar-canales]

Un canal es apropiado cuando quieres separar un ámbito de estado compartido entre participantes.

| Necesidad                                  | ¿Canal?                 |
| ------------------------------------------ | ----------------------- |
| Mundo persistente compartido               | Sí                      |
| Partida independiente                      | Sí                      |
| Evento temporal con jugadores concretos    | Sí                      |
| Simplemente enviar un mensaje a un jugador | No necesariamente       |
| Separar transporte físico                  | No                      |
| Representar una entidad del mundo          | No; utiliza un `Objeto` |

## Relación con TNet [#relación-con-tnet]

El modelo multicanal procede de la arquitectura de TNet y Eco mantiene esta capacidad con su propia API. Para consultar el comportamiento actual, la fuente normativa es `ClienteJuego` y `Canal` dentro de `Nervelink/eco`.

La ventaja de esta documentación frente a los ejemplos antiguos es que **no debemos asumir un único canal activo por cliente**.

## Referencias [#referencias]

<Card title="Canales" href="/docs/red/v1/modelo/canales">
  Referencia completa del ciclo de vida y estado de un canal.
</Card>

<Card title="Cliente" href="/docs/red/v1/runtime/cliente">
  Gestión de conexión y canales desde el cliente.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream de la arquitectura heredada.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Referencia externa para contrastar el modelo de canales de TNet.
</Card>
