# Cliente avanzado (/docs/red/v1/runtime/cliente-avanzado)



# Cliente avanzado [#cliente-avanzado]

`ClienteJuego` centraliza el estado de sesión del cliente: conexión, jugador local, canales, datos del servidor y jugador, TCP, UDP y colas. La implementación expone además el estado de unión a canales y soporte para servidor local sin sockets. fileciteturn216file0

## Estados importantes [#estados-importantes]

```text
Desconectado
   ↓
Intentando conectar
   ↓
Conectado
   ↓
Uniéndose a canales
   ↓
Activo en uno o varios canales
```

No confundas `isConnected`, `isJoiningChannel` e `isInChannel`: representan estados distintos.

## Canales [#canales]

`channels` contiene los canales actuales y `IsJoiningChannel(id)` / `IsInChannel(id)` permiten consultar un canal concreto. Esto es fundamental en un cliente multicanal.

## Datos del jugador [#datos-del-jugador]

`playerData` y `SetPlayerData` proporcionan almacenamiento asociado al jugador, separado del `Nodo` de un objeto. `SyncPlayerData()` permite forzar la sincronización del árbol completo. fileciteturn216file0

## Datos del servidor [#datos-del-servidor]

`serverData` representa configuración global asociada al servidor. No debe utilizarse como sustituto del estado de una partida o del estado individual de un jugador.

## Ping y métricas [#ping-y-métricas]

El cliente expone `ping`, bytes y paquetes enviados/recibidos y el tiempo del servidor. Estos valores son útiles para diagnóstico y telemetría, pero no deben utilizarse directamente como lógica de gameplay sin una estrategia de tolerancia a latencia.

## Desconexión [#desconexión]

La desconexión debe tratarse como un cambio de estado de la sesión. Los objetos y canales locales pueden quedar inválidos aunque referencias de Unity sigan existiendo; la integración debe escuchar los eventos apropiados y limpiar su estado.

## Referencias [#referencias]

<Card title="Cliente" href="/docs/red/v1/runtime/cliente">
  Referencia general del cliente.
</Card>

<Card title="Diagnosticar una conexión" href="/docs/red/v1/guias/diagnosticar-conexion">
  Workflow práctico de diagnóstico.
</Card>
