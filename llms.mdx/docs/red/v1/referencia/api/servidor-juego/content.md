# ServidorJuego (/docs/red/v1/referencia/api/servidor-juego)



# ServidorJuego [#servidorjuego]

`ServidorJuego` concentra la ejecución del servidor de partidas. Administra jugadores, canales, paquetes, persistencia y conexiones TCP/UDP.

## Responsabilidades [#responsabilidades]

```text
ServidorJuego
├── Conexiones
├── Jugadores
├── Canales
├── Paquetes / RFC
├── Persistencia
├── Administración
└── Lobby / extensiones
```

## Ciclo de vida [#ciclo-de-vida]

El servidor prepara su configuración, escucha conexiones y procesa paquetes hasta recibir una orden de parada.

Los callbacks principales son:

```csharp
servidor.onPlayerConnect += OnConnect;
servidor.onPlayerDisconnect += OnDisconnect;
servidor.onShutdown += OnShutdown;
```

## Persistencia [#persistencia]

`saveFile` identifica el archivo de mundo utilizado por el servidor. Las funciones de lectura y escritura pueden sustituirse para almacenar el mundo en otro backend.

`Sleep()` y `Wake()` permiten reducir la memoria utilizada por canales vacíos.

## Multihilo [#multihilo]

`ServidorJuego.isMultiThreaded` indica si está habilitada la ejecución multihilo. El servidor dispone de `SINGLE_THREADED` como modo alternativo.

<Callout title="Hilo del servidor" type="warn">
  No des por supuesto que todos los callbacks del servidor se ejecutan en el hilo principal de Unity. Las extensiones que interactúen con APIs de Unity deben respetar las restricciones de thread safety.
</Callout>

## Paquetes personalizados [#paquetes-personalizados]

Los paquetes que no son procesados por el núcleo pueden llegar a `onCustomPacket`.

```csharp
servidor.onCustomPacket = (jugador, buffer, reader, id, fiable) =>
{
    // procesar extensión
};
```

## Estado global [#estado-global]

El servidor mantiene `serverData` para datos globales que no pertenecen a un canal o jugador concreto.

## Integración de almacenamiento [#integración-de-almacenamiento]

Las funciones `readFunc` y `writeFunc` permiten sustituir el backend de persistencia del mundo. Esto permite conectar el servidor a almacenamiento propio, nube o sistemas de archivos específicos.

## Referencia [#referencia]

<Card title="ServidorJuego.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Servidor/ServidorJuego.cs">
  Código fuente actual.
</Card>

<Card title="Servidor en profundidad" href="/docs/red/v1/runtime/servidor-en-profundidad">
  Explicación arquitectónica del runtime del servidor.
</Card>
