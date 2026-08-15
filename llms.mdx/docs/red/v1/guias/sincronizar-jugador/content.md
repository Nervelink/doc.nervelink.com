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

```csharp
cliente.SetPlayerData("Perfil/Nivel", 12);
cliente.SetPlayerData("Perfil/Clase", "Caballero");
```

## Leer los datos [#leer-los-datos]

```csharp
Nodo datos = cliente.playerData;
```

Cuando necesites leer un dato concreto, trabaja con las operaciones de `Nodo` sobre la ruta correspondiente.

## Sincronización completa [#sincronización-completa]

```csharp
cliente.playerData.SetChild("Perfil/Nivel", 12);
cliente.SyncPlayerData();
```

| Operación                    | Uso                                   |
| ---------------------------- | ------------------------------------- |
| `SetPlayerData(path, value)` | Cambiar y enviar un dato concreto     |
| `SyncPlayerData()`           | Enviar el árbol completo del jugador  |
| `playerData`                 | Leer o manipular localmente el `Nodo` |

## Persistencia [#persistencia]

Los datos del jugador y la persistencia del mundo no son exactamente el mismo sistema. `playerData` representa información del participante; el estado persistente de canales y objetos pertenece al mundo de red.

<Card title="ClienteJuego" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/ClienteJuego.cs">
  Implementación de `playerData`, `SetPlayerData` y `SyncPlayerData`.
</Card>

<Card title="Persistencia" href="/docs/red/v1/persistencia">
  Estado persistente de la sesión y del mundo.
</Card>
