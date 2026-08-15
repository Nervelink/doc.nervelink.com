# Canal (/docs/red/v1/referencia/api/canal)



# Canal [#canal]

`Canal` representa un ámbito lógico de estado compartido. Un mismo cliente puede pertenecer a varios canales y un canal puede contener jugadores, objetos dinámicos, datos y operaciones persistentes.

## Identidad y estado [#identidad-y-estado]

| Miembro        | Propósito                            |
| -------------- | ------------------------------------ |
| `id`           | Identidad del canal                  |
| `level`        | Nivel asociado                       |
| `password`     | Protección de entrada                |
| `isPersistent` | Mantener el canal aunque quede vacío |
| `isClosed`     | Bloquear nuevas entradas             |
| `isLocked`     | Estado de bloqueo del canal          |
| `playerLimit`  | Límite de jugadores                  |
| `jugadores`    | Participantes actuales               |
| `host`         | Anfitrión actual                     |
| `rfcs`         | RFC guardadas                        |
| `created`      | Objetos dinámicos persistentes       |
| `destroyed`    | Objetos dinámicos destruidos         |

## Entrada [#entrada]

Un canal abierto y con plazas disponibles puede aceptar nuevos jugadores.

```csharp
if (canal.isOpen)
{
    // El canal puede aceptar una entrada.
}
```

El hecho de que un canal exista no implica que un cliente concreto ya haya terminado de entrar en él.

## Objetos dinámicos [#objetos-dinámicos]

Eco separa los objetos estáticos de escena de los objetos creados durante el runtime. Los dinámicos necesitan IDs libres dentro del canal y pueden entrar en `created` cuando se persisten.

```text
Canal
├── Objetos de escena
└── Objetos dinámicos
    ├── creados
    └── destruidos
```

## Jugadores y ownership [#jugadores-y-ownership]

Cuando un jugador abandona un canal, los objetos que posee pueden seguir destinos diferentes según su configuración: destrucción, transferencia o persistencia.

Por eso la salida de un jugador debe tratarse como una operación sobre el estado del canal, no sólo sobre la conexión.

## RFC persistentes [#rfc-persistentes]

`rfcs` conserva operaciones necesarias para reconstruir estado del canal. El orden es importante: si una RFC guardada se modifica, Eco puede reubicarla para mantener la secuencia de reconstrucción correcta.

## Transferencia [#transferencia]

La transferencia de un objeto dinámico entre canales cambia su contexto y puede asignarle otro ID. El protocolo debe aceptar durante la transición referencias que todavía utilicen el identificador antiguo.

<Callout title="Sólo objetos dinámicos" type="warn">
  Los objetos estáticos de escena no utilizan el mismo mecanismo de transferencia.
</Callout>

## Sleep y Wake [#sleep-y-wake]

Los canales vacíos pueden pasar a un estado de reposo:

```text
Canal activo
    ↓ último jugador abandona
Sleep()
    ↓
Estado descargado / serializado
    ↓ siguiente entrada
Wake()
    ↓
Canal activo
```

Esto reduce memoria a costa de trabajo adicional cuando el canal vuelve a utilizarse.

## API relacionada [#api-relacionada]

<Card title="Canales" href="/docs/red/v1/modelo/canales">
  Uso de canales desde cliente y gameplay.
</Card>

<Card title="Persistencia" href="/docs/red/v1/persistencia">
  Conservación del estado del canal y sus objetos.
</Card>

<Card title="Canal.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Comun/Canal.cs">
  Implementación actual.
</Card>
