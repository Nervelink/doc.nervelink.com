# ClienteJuego (/docs/red/v1/referencia/api/cliente-juego)



# ClienteJuego [#clientejuego]

`ClienteJuego` contiene la implementación de la sesión cliente que utiliza la fachada `Eco`.

En código de gameplay, normalmente se trabaja con `Eco`. Esta página resulta útil cuando necesitas diagnosticar o extender el runtime.

## Estado de conexión [#estado-de-conexión]

```csharp
cliente.isConnected;
cliente.isTryingToConnect;
cliente.isJoiningChannel;
cliente.isInChannel;
cliente.stage;
```

Estos estados son independientes. Estar conectado al servidor no implica pertenecer a un canal.

## Canales [#canales]

```csharp
cliente.channels;
cliente.IsInChannel(idCanal);
cliente.IsJoiningChannel(idCanal);
cliente.GetChannel(idCanal);
cliente.JoinChannel(...);
cliente.LeaveChannel(idCanal);
```

`channels` es la colección de canales activos de la conexión.

## Jugador local [#jugador-local]

```csharp
cliente.playerID;
cliente.playerName;
cliente.playerData;
cliente.jugador;
```

El cliente también proporciona métodos para modificar datos del jugador y del servidor.

## Transporte [#transporte]

```csharp
cliente.protocol;
cliente.custom;
cliente.canUseUDP;
cliente.listeningPort;
cliente.tcpEndPoint;
cliente.ping;
```

TCP es el transporte principal y UDP es opcional. `custom` permite sustituir la capa de conexión mediante `IConnection`.

## Diagnóstico [#diagnóstico]

```csharp
cliente.sentPackets;
cliente.sentBytes;
cliente.receivedPackets;
cliente.receivedBytes;
cliente.serverTime;
cliente.serverUptime;
```

Estas métricas permiten detectar saturación, tráfico inesperado y problemas de latencia.

## Colas [#colas]

El cliente expone colas de recepción y envío para integraciones que operan sin sockets directamente. Deben tratarse como estructuras internas y sincronizarse correctamente cuando se usan desde hilos externos.

## Paquetes personalizados [#paquetes-personalizados]

`packetHandlers` permite registrar controladores para paquetes que el núcleo no procesa.

```csharp
cliente.packetHandlers[(byte)miPaquete] = OnPacket;
```

## Referencia [#referencia]

<Card title="ClienteJuego.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/ClienteJuego.cs">
  Código fuente actual.
</Card>

<Card title="Eco" href="/docs/red/v1/referencia/api/eco">
  Fachada recomendada para la mayoría del código del juego.
</Card>
