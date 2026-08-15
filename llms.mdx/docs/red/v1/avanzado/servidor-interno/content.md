# Servidor interno (/docs/red/v1/avanzado/servidor-interno)



# Servidor interno [#servidor-interno]

`Servidor` y `ServidorJuego` forman el lado servidor del runtime de Eco. El primero integra el servidor con Unity; el segundo contiene la lógica de juego y gestión de clientes, canales y estado.

## Capas [#capas]

```text
Eco
 │
 └── Servidor
       │
       └── ServidorJuego
             ├── Jugadores
             ├── Canales
             ├── Objetos
             ├── Datos
             ├── Persistencia
             └── Protocolos TCP / UDP
```

`ServidorJuego` puede funcionar como servidor local o como núcleo independiente dentro de un proceso servidor.

## Flujo de una conexión [#flujo-de-una-conexión]

<Steps>
  <Step>
    ### Aceptar la conexión [#aceptar-la-conexión]

    El protocolo TCP establece la conexión inicial y crea el contexto del cliente.
  </Step>

  <Step>
    ### Verificar al jugador [#verificar-al-jugador]

    Se negocian versión, identidad y datos iniciales antes de entrar en la lógica normal de juego.
  </Step>

  <Step>
    ### Crear o recuperar la participación [#crear-o-recuperar-la-participación]

    El servidor representa al cliente como `Jugador` y gestiona su pertenencia a `Canal`.
  </Step>

  <Step>
    ### Procesar paquetes [#procesar-paquetes]

    Las solicitudes se convierten en operaciones de canal, objetos, datos, RFC, archivos o control del servidor.
  </Step>
</Steps>

## Canales y autoridad [#canales-y-autoridad]

El servidor mantiene los canales como unidades lógicas aisladas. Cada canal tiene sus propios jugadores, objetos y datos.

```text
Servidor
├── Canal 100
│   ├── Jugadores
│   ├── Objetos
│   └── Datos
│
└── Canal 200
    ├── Jugadores
    ├── Objetos
    └── Datos
```

La conexión de un jugador puede participar en varios canales, por lo que el servidor no debe modelar un jugador como perteneciente exclusivamente a una única sala.

## Persistencia [#persistencia]

El servidor puede guardar estado de canales, objetos persistentes, datos de jugadores y operaciones remotas guardadas. Esto explica por qué Eco no trata la red y la persistencia como dos sistemas completamente separados.

<Callout title="Persistencia por capas" type="idea">
  No todo lo que existe en memoria debe guardarse. La persistencia depende de si el canal, objeto, dato o RFC ha sido marcado para conservarse.
</Callout>

## Modo de baja memoria [#modo-de-baja-memoria]

El material de TNet documenta un modo de bajo consumo de memoria que descarga datos de canales vacíos para reducir el footprint del servidor. En Eco debe considerarse una característica dependiente de la implementación actual del servidor antes de habilitarse en producción.

La idea es:

```text
Último jugador abandona
        ↓
Canal sin participantes
        ↓
Guardar estado
        ↓
Liberar memoria de la representación activa
        ↓
Recargar cuando vuelva un jugador
```

## Servidor local [#servidor-local]

El servidor interno también puede ejecutarse dentro del mismo proceso de Unity. Esto permite conectar un `ClienteJuego` contra el servidor local sin depender de sockets externos.

<Card title="Servidor local" href="/docs/red/v1/guias/servidor-local">
  Workflow práctico para pruebas locales.
</Card>

## Código fuente [#código-fuente]

<Card title="ServidorJuego.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Servidor/ServidorJuego.cs">
  Implementación principal del servidor de juego.
</Card>

<Card title="Servidor.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Servidor/Servidor.cs">
  Integración del servidor con el runtime de Unity.
</Card>
