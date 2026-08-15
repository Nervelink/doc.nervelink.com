# Cliente (/docs/red/v1/runtime/cliente)



# Cliente [#cliente]

`ClienteJuego` contiene la lógica cliente de Eco: conexión, procesamiento de paquetes, canales, jugador local, transporte y modo servidor local.

<Callout title="Fachada frente a implementación" type="info">
  Desde gameplay normalmente trabajarás con la fachada `Eco` y componentes de alto nivel. `ClienteJuego` es el runtime que mantiene el estado y ejecuta las operaciones de red.
</Callout>

## Anatomía [#anatomía]

<Cards>
  <Card title="Conexión">
    Gestiona TCP y las etapas del handshake.
  </Card>

  <Card title="Canales">
    Mantiene los canales activos y las solicitudes de unión pendientes.
  </Card>

  <Card title="Jugador local">
    Expone identidad, nombre y `playerData` del participante local.
  </Card>

  <Card title="Transporte">
    Utiliza TCP como camino principal, UDP cuando está disponible y `IConnection` para conexiones personalizadas.
  </Card>
</Cards>

## Estados de sesión [#estados-de-sesión]

```text
isTryingToConnect
        │
        ▼
   isConnected
        │
        ├── isJoiningChannel
        │        │
        │        ▼
        └──── isInChannel
```

Estos estados no son intercambiables. Estar conectado no implica pertenecer a un canal.

## Conexión [#conexión]

<div className="fd-steps">
  <div className="fd-step">
    ### Iniciar [#iniciar-step]

    ```csharp
    cliente.Connect(endpoint);
    ```
  </div>

  <div className="fd-step">
    ### Esperar el handshake [#esperar-el-handshake-step]

    `Connect` sólo inicia el proceso. Comprueba `isConnected` antes de usar operaciones que requieren una sesión confirmada.
  </div>

  <div className="fd-step">
    ### Comprobar el estado [#comprobar-el-estado-step]

    ```csharp
    if (cliente.isConnected)
    {
        // Puede solicitarse una operación dependiente del servidor.
    }
    ```
  </div>
</div>

## Canales [#canales]

El cliente mantiene una colección de canales y otra de identificadores que todavía se están uniendo.

```csharp
bool dentro = cliente.IsInChannel(10);
bool pendiente = cliente.IsJoiningChannel(10);
Canal canal = cliente.GetChannel(10);
```

Puede participar simultáneamente en varios canales:

```csharp
cliente.JoinChannel(10, "Mundo", true, 100, "");
cliente.JoinChannel(20, "Partida", false, 4, "");
```

Consulta [Canales](/docs/red/v1/modelo/canales) para entender qué significa esta pertenencia.

## Transporte [#transporte]

<Tabs items="['TCP', 'UDP', 'IConnection']">
  <Tab value="TCP">
    Es el transporte principal de la sesión y del protocolo fiable.
  </Tab>

  <Tab value="UDP">
    Es opcional y se utiliza cuando la configuración y la plataforma permiten tráfico de baja latencia.
  </Tab>

  <Tab value="IConnection">
    Permite abstraer la conexión subyacente para integraciones personalizadas que no utilizan directamente el socket estándar.
  </Tab>
</Tabs>

## Procesamiento de paquetes [#procesamiento-de-paquetes]

El cliente recibe datos y los convierte en `Paquete` y operaciones de runtime.

```text
bytes
  ↓
Buffer
  ↓
Paquete
  ↓
ClienteJuego
  ↓
Canal / Objeto / Jugador
```

`ClienteJuego` mantiene además contadores de paquetes y bytes enviados/recibidos. Su implementación contempla opciones para limitar y perfilar el trabajo realizado por frame.

## Tiempo y diagnóstico [#tiempo-y-diagnóstico]

El cliente expone información como:

```csharp
cliente.serverTime;
cliente.serverUptime;
cliente.ping;
cliente.sentPackets;
cliente.receivedPackets;
```

Estos valores son útiles para diagnóstico, pero no deben utilizarse como fuente única de verdad para reglas de gameplay.

## Datos del jugador [#datos-del-jugador]

El cliente distingue entre datos del objeto y datos del participante:

```csharp
cliente.playerData;
cliente.SetPlayerData("perfil/nivel", 12);
cliente.SyncPlayerData();
```

Consulta [Sincronizar los datos del jugador](/docs/red/v1/guias/sincronizar-jugador).

## Servidor local [#servidor-local]

`ClienteJuego` puede utilizar un `ServidorJuego` local sin sockets. Esto conserva el flujo lógico de cliente-servidor, pero no debe utilizarse como única validación de comportamiento de red real.

<Card title="Guía: Servidor local" href="/docs/red/v1/guias/servidor-local">
  Workflow para levantar cliente y servidor dentro del mismo proceso.
</Card>

## Desconexión [#desconexión]

```csharp
cliente.Disconnect();
```

La desconexión cierra la sesión y permite que el runtime libere sus dependencias de transporte.

<Callout title="No limpies el estado a mano sin entender el runtime" type="warn">
  Si tu juego mantiene referencias propias a jugadores, canales u objetos, enlázalas a los callbacks de ciclo de vida de Eco en lugar de asumir que el orden de destrucción de Unity será suficiente.
</Callout>

## Correspondencia con TNet [#correspondencia-con-tnet]

| Eco            | TNet          | Responsabilidad |
| -------------- | ------------- | --------------- |
| `ClienteJuego` | `GameClient`  | Runtime cliente |
| `ProtocoloTcp` | `TcpProtocol` | Transporte TCP  |
| `ProtocoloUdp` | `UdpProtocol` | Transporte UDP  |
| `Jugador`      | `Player`      | Participante    |

## Referencias [#referencias]

<Cards>
  <Card title="Primer cliente" href="/docs/red/v1/guias/primer-cliente">
    Integración mínima de cliente.
  </Card>

  <Card title="Transporte" href="/docs/red/v1/transporte">
    Protocolos y serialización.
  </Card>

  <Card title="Eco" href="https://github.com/Nervelink/eco">
    Implementación actual.
  </Card>
</Cards>
