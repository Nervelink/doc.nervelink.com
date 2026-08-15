# Sincronizar los datos del jugador (/docs/red/v1/guias/sincronizar-jugador)



# Sincronizar los datos del jugador [#sincronizar-los-datos-del-jugador]

Eco separa los datos del jugador de los datos de un `Objeto`. Esto permite almacenar información asociada directamente al participante de la sesión sin convertirla en estado de una entidad del mundo.

<Callout title="Jugador ≠ Objeto" type="info">
  Utiliza `playerData` para información del participante y `Objeto` para entidades de red del mundo. No mezcles ambos modelos sólo porque ambos utilizan `Nodo`.
</Callout>

## Modelo [#modelo]

```text
ClienteJuego
    │
    └── Jugador local
            │
            └── playerData : Nodo
```

El jugador local expone sus datos mediante `playerData`, mientras que `SetPlayerData` permite modificar una ruta concreta y solicitar su sincronización con el servidor. `SyncPlayerData` permite forzar la sincronización completa del `Nodo`.

## Asignar un dato [#asignar-un-dato]

Para cambiar un valor concreto utiliza `SetPlayerData`:

```csharp
cliente.SetPlayerData("Perfil/Nivel", 12);
cliente.SetPlayerData("Perfil/Clase", "Caballero");
```

La ruta se almacena dentro del `Nodo` del jugador y Eco envía la operación al servidor cuando existe una conexión activa.

## Leer los datos [#leer-los-datos]

Puedes consultar el árbol completo mediante `playerData`:

```csharp
Nodo datos = cliente.playerData;
```

Cuando necesites leer un dato concreto, trabaja con las operaciones de `Nodo` sobre la ruta correspondiente.

## Sincronización completa [#sincronización-completa]

Si modificas directamente el `Nodo` y necesitas enviar su estado completo, utiliza `SyncPlayerData`:

```csharp
cliente.playerData.SetChild("Perfil/Nivel", 12);
cliente.SyncPlayerData();
```

La diferencia práctica es:

| Operación                    | Uso                                   |
| ---------------------------- | ------------------------------------- |
| `SetPlayerData(path, value)` | Cambiar y enviar un dato concreto     |
| `SyncPlayerData()`           | Enviar el árbol completo del jugador  |
| `playerData`                 | Leer o manipular localmente el `Nodo` |

## Reacción a cambios [#reacción-a-cambios]

`SetPlayerData` también puede disparar el callback de cambios de datos del jugador cuando está registrado.

Esto permite separar el almacenamiento de datos de la lógica de juego:

```text
SetPlayerData
     │
     ├── actualiza Nodo local
     ├── solicita sincronización
     └── notifica cambio
```

El callback puede utilizarse para actualizar UI, estadísticas o sistemas que dependan del cambio recibido.

## Qué guardar aquí [#qué-guardar-aquí]

Los datos del jugador son apropiados para información ligada al participante:

```text
Perfil
├── Nombre
├── Nivel
├── Clase
└── Preferencias
```

No son el lugar adecuado para representar directamente entidades del mundo:

```text
Incorrecto
Jugador
└── PosicionEnemigo
```

Para ese caso debe utilizarse un `Objeto` con su estado correspondiente.

## Jugadores remotos [#jugadores-remotos]

El mismo concepto de datos puede aplicarse a jugadores observados por el cliente. La lógica que maneje `Jugador` puede reaccionar a cambios recibidos sin asumir que esos datos pertenecen al jugador local.

Conviene distinguir siempre:

```text
Eco.Jugador
    ↓
jugador local

Jugador de otro participante
    ↓
estado remoto
```

## Persistencia [#persistencia]

Los datos del jugador y la persistencia del mundo no son exactamente el mismo sistema. Aunque ambos utilizan estructuras de datos de Eco, la persistencia de un canal o de sus objetos pertenece al estado del mundo, mientras que `playerData` representa información del participante.

Consulta la guía [Persistir una partida](/docs/red/v1/guias/persistir-partida) cuando necesites conservar estado del mundo.

## Errores habituales [#errores-habituales]

### Escribir directamente en `playerData` y no sincronizar [#escribir-directamente-en-playerdata-y-no-sincronizar]

Modificar el `Nodo` local no implica automáticamente que el servidor reciba el cambio. Si la modificación se realiza directamente sobre el árbol, utiliza `SyncPlayerData()` cuando necesites enviar el estado resultante.

### Usar `playerData` para entidades del mundo [#usar-playerdata-para-entidades-del-mundo]

El jugador no debe convertirse en un contenedor arbitrario de todas las entidades que posee. Utiliza objetos de red para representar entidades con identidad, canal y ciclo de vida propios.

### Confundir identidad con nombre [#confundir-identidad-con-nombre]

`playerID` identifica al jugador en la sesión. `playerName` es información editable y no debe utilizarse como identificador de red.

## Relación con TNet [#relación-con-tnet]

La arquitectura conserva el modelo de datos del jugador de TNet, pero Eco expone la API mediante `ClienteJuego`, `Jugador` y `Nodo`.

La referencia normativa para esta API es el código actual de `Nervelink/eco`.

## Referencias [#referencias]

<Card title="ClienteJuego" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/ClienteJuego.cs">
  Implementación de `playerData`, `SetPlayerData` y `SyncPlayerData`.
</Card>

<Card title="Conceptos" href="/docs/red/v1/fundamentos/conceptos">
  Modelo general de jugadores, objetos, canales y componentes.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream de la arquitectura heredada.
</Card>
