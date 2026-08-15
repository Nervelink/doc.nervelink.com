# ClienteJuego (/docs/red/v1/referencia/api/cliente-juego)



# ClienteJuego [#clientejuego]

`ClienteJuego` es la implementación interna de la sesión cliente. La mayoría del gameplay debe utilizar `Eco`, pero esta clase es fundamental para integrar sistemas avanzados, depurar el runtime y trabajar con transportes personalizados.

## Estado de sesión [#estado-de-sesión]

| Miembro             | Significado                      |
| ------------------- | -------------------------------- |
| `isConnected`       | Conexión establecida             |
| `isTryingToConnect` | Intentando conectar              |
| `isJoiningChannel`  | Existe una entrada pendiente     |
| `isInChannel`       | Pertenece al contexto consultado |
| `stage`             | Etapa interna del cliente        |
| `playerID`          | ID local                         |
| `playerName`        | Nombre actual                    |
| `playerData`        | Nodo de datos del jugador        |

Conectado y dentro de un canal son estados diferentes.

## Canales [#canales]

```csharp
cliente.IsInChannel(idCanal);
cliente.IsJoiningChannel(idCanal);
cliente.GetChannel(idCanal);
cliente.JoinChannel(...);
cliente.LeaveChannel(idCanal);
```

La colección `channels` representa los canales activos del cliente. El modelo permite varios canales simultáneos.

## Transporte [#transporte]

```csharp
cliente.protocol;
cliente.custom;
cliente.canUseUDP;
cliente.listeningPort;
cliente.tcpEndPoint;
cliente.ping;
```

`custom` permite sustituir la conexión mediante `IConnection` sin cambiar el modelo lógico de canales y objetos.

## Métricas [#métricas]

```csharp
cliente.sentPackets;
cliente.sentBytes;
cliente.receivedPackets;
cliente.receivedBytes;
cliente.serverTime;
cliente.serverUptime;
```

Estas propiedades son útiles para instrumentación y para comprobar si un problema está en gameplay, procesamiento o transporte.

## Cola de paquetes [#cola-de-paquetes]

El cliente recibe datos del transporte y los procesa durante su ciclo de actualización. El procesamiento distribuido evita que una ráfaga de paquetes bloquee indefinidamente el frame.

```text
Socket / IConnection
        ↓
Buffer
        ↓
Cola de entrada
        ↓
ProcesarPaquetes
        ↓
Despacho
```

## Paquetes personalizados [#paquetes-personalizados]

```csharp
cliente.packetHandlers[(byte)miPaquete] = OnPacket;
```

El handler debe respetar el formato binario definido por quien genera el paquete.

## Relación con Eco [#relación-con-eco]

```text
Gameplay
   ↓
Eco
   ↓
ClienteJuego
   ├── Transporte
   ├── Canales
   ├── Jugador
   ├── Paquetes
   └── Objetos
```

<Callout title="No expongas ClienteJuego innecesariamente" type="warn">
  Encapsula el acceso directo dentro de sistemas de infraestructura. El gameplay normal debe permanecer sobre la fachada de Eco para evitar acoplamiento al runtime.
</Callout>

## Código fuente [#código-fuente]

<Card title="ClienteJuego.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/ClienteJuego.cs">
  Implementación actual del runtime cliente.
</Card>

<Card title="Eco" href="/docs/red/v1/referencia/api/eco">
  Fachada recomendada para gameplay.
</Card>
