# Canales (/docs/red/v1/modelo/canales)



# Canales [#canales]

Un `Canal` define un ámbito de estado compartido dentro de Eco. Agrupa jugadores, objetos, nivel, datos y operaciones persistentes que pertenecen a una misma sesión lógica.

<Callout title="Canal ≠ conexión" type="warn">
  Una conexión física puede participar en varios canales simultáneamente. Nunca modeles un canal como si fuera un socket.
</Callout>

## Qué contiene un canal [#qué-contiene-un-canal]

<Cards>
  <Card title="Jugadores">
    Participantes que pertenecen al ámbito del canal y pueden recibir su estado.
  </Card>

  <Card title="Objetos">
    Entidades de red cuyo `channelID` identifica el contexto donde viven.
  </Card>

  <Card title="Estado">
    Nivel, datos del canal y RFC/estado que deben poder reconstruirse.
  </Card>

  <Card title="Autoridad">
    Host, ownership y reglas de modificación que dependen del contexto del canal.
  </Card>
</Cards>

## Modelo mental [#modelo-mental]

```text
Conexión
│
├── Canal 10 · Mundo
│   ├── Jugador A
│   ├── Jugador B
│   └── Objetos
│
├── Canal 20 · Partida
│   ├── Jugador A
│   └── Objetos
│
└── Canal 30 · Evento
    └── Jugador C
```

## Estado relevante [#estado-relevante]

La implementación mantiene propiedades como `id`, `level`, `isPersistent`, `isClosed`, `isLeaving`, `playerLimit`, `jugadores`, `rfcs`, `created`, `destroyed` y `host`.

No todas estas propiedades deben ser manipuladas directamente. Parte de ellas representan el estado interno que mantiene Eco durante las transiciones.

## Entrada al canal [#entrada-al-canal]

<div className="fd-steps">
  <div className="fd-step">
    ### Solicitar la entrada \[step] [#1-solicitar-la-entrada-step]

    ```csharp
    cliente.JoinChannel(
        channelID: 10,
        levelName: "Arena",
        persistent: false,
        playerLimit: 4,
        password: ""
    );
    ```
  </div>

  <div className="fd-step">
    ### Esperar la confirmación \[step] [#2-esperar-la-confirmación-step]

    Eco registra el identificador como pendiente mientras espera la respuesta del servidor. Durante esta fase no debes tratar el canal como completamente operativo.
  </div>

  <div className="fd-step">
    ### Comprobar la pertenencia \[step] [#3-comprobar-la-pertenencia-step]

    ```csharp
    if (cliente.IsInChannel(10))
    {
        Canal canal = cliente.GetChannel(10);
    }
    ```

    `IsJoiningChannel(10)` permite diferenciar el estado pendiente del estado confirmado.
  </div>
</div>

## Varios canales [#varios-canales]

```csharp title="Mundo + partida"
cliente.JoinChannel(10, "Mundo", true, 100, "");
cliente.JoinChannel(20, "Partida", false, 4, "");
```

```text
Jugador A
   │
   ├── Canal 10 → mundo persistente
   └── Canal 20 → partida actual
```

Consulta la guía [Varios canales simultáneos](/docs/red/v1/guias/multiples-canales) para un workflow completo.

## Salir de un canal [#salir-de-un-canal]

```csharp
cliente.LeaveChannel(20);
```

Abandonar un canal no desconecta al cliente y no elimina automáticamente su pertenencia a otros canales.

Para cerrar todos los contextos activos:

```csharp
cliente.LeaveAllChannels();
```

## Nivel, host y persistencia [#nivel-host-y-persistencia]

El canal también es el contexto de operaciones coordinadas como el cambio de nivel y la persistencia.

<Tabs items="['Temporal', 'Persistente']">
  <Tab value="Temporal">
    El canal representa una sesión que puede cerrarse y liberar su estado cuando ya no quedan participantes y no existen condiciones que lo mantengan abierto.
  </Tab>

  <Tab value="Persistente">
    El canal puede mantener su estado sin jugadores activos para poder ser recuperado posteriormente.
  </Tab>
</Tabs>

## Reglas de diseño [#reglas-de-diseño]

<Callout title="Diseña por ámbito" type="idea">
  Si dos sistemas necesitan ciclos de vida diferentes, considera separarlos en canales distintos. Si sólo necesitas enviar una orden diferente a un jugador, probablemente no necesitas otro canal.
</Callout>

| Necesidad                 | Canal             |
| ------------------------- | ----------------- |
| Mundo persistente         | Sí                |
| Partida independiente     | Sí                |
| Evento temporal aislado   | Sí                |
| Mensaje a un solo jugador | No necesariamente |
| Separar TCP/UDP           | No                |
| Identificar una entidad   | No; usa `Objeto`  |

## Correspondencia con TNet [#correspondencia-con-tnet]

| TNet                     | Eco                         |
| ------------------------ | --------------------------- |
| `Channel`                | `Canal`                     |
| `TNManager.JoinChannel`  | `ClienteJuego.JoinChannel`  |
| `TNManager.LeaveChannel` | `ClienteJuego.LeaveChannel` |
| `TNManager.IsInChannel`  | `ClienteJuego.IsInChannel`  |
| `TNManager.GetChannel`   | `ClienteJuego.GetChannel`   |

## Referencias [#referencias]

<Cards>
  <Card title="Modelo de objetos" href="/docs/red/v1/modelo/objetos">
    Cómo se relacionan objeto, canal, jugador y ownership.
  </Card>

  <Card title="Varios canales" href="/docs/red/v1/guias/multiples-canales">
    Ejemplo práctico de una conexión participando en varios canales.
  </Card>

  <Card title="Canal.cs · Eco" href="https://github.com/Nervelink/eco/tree/main/src/Assets/Pandora/Logica/Nucleo/Core/Red">
    Implementación actual del modelo de canal.
  </Card>
</Cards>
