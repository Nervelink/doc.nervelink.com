# Servidor en profundidad (/docs/red/v1/runtime/servidor-en-profundidad)



# Servidor en profundidad [#servidor-en-profundidad]

`ServidorJuego` concentra la lógica de servidor: conexiones, jugadores, canales, RFC, persistencia, transporte y administración. La implementación actual mantiene colecciones separadas para jugadores y canales y utiliza TCP como transporte principal, con UDP opcional. fileciteturn233file0

## Responsabilidades [#responsabilidades]

```text
ServidorJuego
├── Conexiones
├── Jugadores
├── Canales
├── RFC / paquetes
├── Persistencia
├── TCP / UDP
└── Administración
```

Esto es importante para no trasladar lógica de servidor al cliente por comodidad.

## Ciclo de vida [#ciclo-de-vida]

El servidor puede iniciarse mediante su API de arranque, aceptar conexiones, autenticar jugadores y permanecer activo hasta recibir una orden de parada. Los callbacks `onPlayerConnect`, `onPlayerDisconnect` y `onShutdown` permiten conectar lógica del juego al ciclo de vida. fileciteturn233file0

## Jugadores y canales [#jugadores-y-canales]

El servidor mantiene una lista y un diccionario de jugadores, y otra pareja para canales activos. Esto permite iteración ordenada y búsquedas por ID sin mezclar ambos conceptos. fileciteturn233file0

Un jugador puede pertenecer a varios canales; por ello el servidor debe evaluar el canal concreto de cada operación.

## Persistencia [#persistencia]

`ServidorJuego` hereda de `ArchivoServidor` y mantiene un archivo de mundo, funciones de lectura/escritura reemplazables y un ciclo de guardado. También dispone de `Sleep(bool)` para liberar memoria de canales sin jugadores y `Wake()` para restaurarlos. fileciteturn233file0

## Multihilo [#multihilo]

El servidor puede ejecutarse multihilo. `isMultiThreaded` refleja la configuración de compilación. Cualquier integración que acceda directamente a estructuras del servidor debe respetar esta realidad y no asumir que todo ocurre en el hilo principal de Unity. fileciteturn233file0

## Paquetes personalizados [#paquetes-personalizados]

Los paquetes que no procesa el servidor por defecto pueden llegar a `onCustomPacket`. Esto permite extender el protocolo sin modificar el núcleo, siempre que la extensión mantenga un contrato claro de serialización y seguridad. fileciteturn233file0

## Estado del servidor [#estado-del-servidor]

El servidor también mantiene `serverData`, que es distinto de los datos de un jugador y de los datos de un canal. El ámbito debe quedar claro en el diseño:

```text
Servidor
└── Datos globales

Canal
└── Datos del canal

Jugador
└── Datos del jugador

Objeto
└── Datos del objeto
```

## Referencias [#referencias]

<Card title="Persistencia" href="/docs/red/v1/persistencia">
  Guardar y restaurar el estado del servidor.
</Card>

<Card title="Servidor" href="/docs/red/v1/runtime/servidor">
  Referencia general de `ServidorJuego`.
</Card>
