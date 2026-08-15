# ServidorJuego (/docs/red/v1/referencia/api/servidor-juego)



# ServidorJuego [#servidorjuego]

`ServidorJuego` concentra el runtime de una instancia de servidor de partidas. Administra conexiones, jugadores, canales, paquetes, persistencia y extensiones.

## Responsabilidades [#responsabilidades]

```text
ServidorJuego
├── Conexiones TCP / UDP
├── Jugadores
├── Canales
├── Paquetes y RFC
├── Persistencia
├── Datos de servidor
├── Archivos
└── Extensiones
```

## Estado y ciclo de vida [#estado-y-ciclo-de-vida]

```csharp
servidor.isRunning;
servidor.isMultiThreaded;
servidor.saveFile;
servidor.serverData;
```

El servidor prepara la configuración, escucha conexiones y entra en su ciclo de procesamiento hasta la orden de parada.

## Eventos [#eventos]

Los puntos de extensión más importantes incluyen conexión, desconexión, administración y apagado:

```csharp
servidor.onPlayerConnect += OnConnect;
servidor.onPlayerDisconnect += OnDisconnect;
servidor.onShutdown += OnShutdown;
```

El conjunto exacto de callbacks debe consultarse en `ServidorJuego.cs`, porque forma parte de la API de runtime y puede evolucionar.

## Persistencia [#persistencia]

`saveFile` identifica el almacenamiento utilizado para el estado del servidor. `readFunc` y `writeFunc` permiten sustituir el mecanismo de lectura y escritura.

Los canales vacíos pueden utilizar `Sleep()` y `Wake()` para descargar o recuperar su representación persistente.

## Multihilo [#multihilo]

`isMultiThreaded` permite saber si el runtime utiliza procesamiento concurrente. Existe una configuración `SINGLE_THREADED` para integraciones que requieren evitar el hilo de red dedicado.

<Callout title="Unity y servidor" type="warn">
  No asumas que un callback del servidor puede tocar directamente cualquier API de Unity. El trabajo recibido desde hilos de red debe trasladarse al contexto apropiado antes de manipular objetos de Unity.
</Callout>

## Paquetes personalizados [#paquetes-personalizados]

Los paquetes no consumidos por el núcleo pueden procesarse mediante `onCustomPacket`:

```csharp
servidor.onCustomPacket = (jugador, buffer, reader, id, fiable) =>
{
    // validar origen
    // leer Buffer
    // ejecutar operación
};
```

La validación del jugador y del canal debe realizarse antes de ejecutar acciones que afecten al estado de la partida.

## Datos de servidor [#datos-de-servidor]

`serverData` representa el estado global que no pertenece a un canal o jugador concreto. Para datos específicos de una partida utiliza `Canal`; para datos del participante utiliza `Jugador`.

## Arquitectura [#arquitectura]

```text
ClienteJuego
     │
 TCP/UDP/IConnection
     ↓
ServidorJuego
 ├── Sesiones
 ├── Canales
 │    ├── Jugadores
 │    ├── Objetos
 │    └── Estado persistente
 └── Protocolo
```

## Fuente [#fuente]

<Card title="ServidorJuego.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Servidor/ServidorJuego.cs">
  Implementación actual.
</Card>

<Card title="Servidor.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Servidor/Servidor.cs">
  Capa de servidor y gestión del runtime.
</Card>

<Card title="Servidor en profundidad" href="/docs/red/v1/runtime/servidor-en-profundidad">
  Arquitectura interna del servidor.
</Card>
