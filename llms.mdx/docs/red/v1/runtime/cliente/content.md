# Cliente (/docs/red/v1/runtime/cliente)



# Cliente [#cliente]

El cliente mantiene la conexión, procesa paquetes y conserva el estado local de la sesión.

> El uso habitual de Eco se apoya en su API pública; `ClienteJuego` contiene gran parte de la implementación interna.

## Flujo [#flujo]

```text
Juego
  ↓
Eco
  ↓
ClienteJuego
  ├── TCP
  ├── UDP
  └── IConnection
       ↓
    Servidor
```

## Estados [#estados]

`isConnected`, `isTryingToConnect`, `isJoiningChannel`, `isInChannel` y `stage` representan estados distintos de la sesión. Eco mantiene además colecciones separadas de canales activos y de uniones pendientes.

## Conexión [#conexión]

TCP es el transporte principal. Eco puede utilizar UDP cuando está disponible y admite una conexión personalizada mediante `IConnection`.

## Procesamiento [#procesamiento]

Los paquetes entrantes se procesan identificando su `Paquete` y ejecutando la operación correspondiente. El cliente también mantiene estadísticas de bytes y paquetes y puede limitar el tiempo de procesamiento por frame.

## Canales [#canales]

El cliente puede participar en varios canales simultáneamente. `JoinChannel`, `LeaveChannel`, `LeaveAllChannels`, `IsInChannel`, `IsJoiningChannel` y `GetChannel` gestionan esta pertenencia.

## Jugador local [#jugador-local]

El cliente expone `playerID`, `playerName`, `playerData`, `serverTime`, `serverUptime` y el acceso a sus canales.

## Servidor local [#servidor-local]

Eco puede conectar un `ClienteJuego` directamente con un `ServidorJuego` dentro del mismo proceso, evitando sockets pero conservando el flujo lógico cliente-servidor.

## Referencia a TNet [#referencia-a-tnet]

| Eco            | TNet          |
| -------------- | ------------- |
| `ClienteJuego` | `GameClient`  |
| `ProtocoloTcp` | `TcpProtocol` |
| `ProtocoloUdp` | `UdpProtocol` |
| `Jugador`      | `Player`      |

<Card title="Eco" href="https://github.com/Nervelink/eco">
  Código fuente de Eco.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream.
</Card>
