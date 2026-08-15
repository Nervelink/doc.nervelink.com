# Servidor (/docs/red/v1/runtime/servidor)



# Servidor [#servidor]

`ServidorJuego` concentra la lógica autoritativa de Eco: conexiones, jugadores, canales, objetos, paquetes, persistencia y servicios auxiliares.

<Callout title="Principio de autoridad" type="info">
  El cliente puede solicitar una operación, pero el servidor es quien debe decidir si esa operación modifica el estado compartido cuando las reglas del juego requieren autoridad central.
</Callout>

## Responsabilidades [#responsabilidades]

<Cards>
  <Card title="Conexiones">
    Acepta jugadores y mantiene sus estados de sesión.
  </Card>

  <Card title="Canales">
    Mantiene los ámbitos activos y coordina jugadores, objetos y estado.
  </Card>

  <Card title="Persistencia">
    Guarda y restaura el estado del mundo y puede dormir canales sin jugadores.
  </Card>

  <Card title="Extensión">
    Permite interceptar paquetes no gestionados mediante `onCustomPacket`.
  </Card>
</Cards>

## Ciclo de vida [#ciclo-de-vida]

<div className="fd-steps">
  <div className="fd-step">
    ### Preparación [#preparación-step]

    Configura identidad del servidor, persistencia, administración y transporte.
  </div>

  <div className="fd-step">
    ### Arranque [#arranque-step]

    `Start()` prepara TCP y el UDP opcional y activa el procesamiento de red.
  </div>

  <div className="fd-step">
    ### Aceptación [#aceptación-step]

    `Listen()` mantiene la escucha de nuevas conexiones TCP y crea el estado asociado al participante.
  </div>

  <div className="fd-step">
    ### Operación [#operación-step]

    El servidor procesa paquetes, actualiza jugadores y canales y ejecuta las operaciones autoritativas.
  </div>

  <div className="fd-step">
    ### Cierre [#cierre-step]

    La detención del servidor finaliza las conexiones y permite guardar el estado si la configuración lo requiere.
  </div>
</div>

## Jugadores [#jugadores]

El servidor mantiene estructuras separadas para iterar y para resolver participantes por ID o endpoint.

```text
Jugador
├── ID
├── Endpoint
├── Nombre
├── Datos
└── Canales
```

Esto permite que un jugador sea tratado como participante lógico independientemente de la representación física de la conexión.

## Canales [#canales]

Los canales activos se almacenan en listas y diccionarios para combinar iteración y acceso rápido.

```text
ServidorJuego
├── Canal 10
│   ├── jugadores
│   ├── objetos
│   └── estado
└── Canal 20
    ├── jugadores
    ├── objetos
    └── estado
```

El mismo jugador puede pertenecer a más de un canal.

## Persistencia y memoria [#persistencia-y-memoria]

`ServidorJuego` dispone de un archivo de mundo y de funciones reemplazables de lectura/escritura.

`Sleep()` reduce la memoria utilizada por canales sin jugadores y `Wake()` permite restaurarlos. El servidor también puede realizar este tipo de reducción durante procesos de guardado.

<Callout title="Persistencia no significa mantener todo en RAM" type="idea">
  Un canal persistente puede existir lógicamente aunque no todos sus datos permanezcan activos en memoria todo el tiempo.
</Callout>

## Procesamiento multihilo [#procesamiento-multihilo]

La implementación contempla ejecución multihilo y expone `isMultiThreaded` según la configuración de compilación.

<Tabs items="['Multihilo', 'Un solo hilo']">
  <Tab value="Multihilo">
    Es el comportamiento normal. El servidor puede procesar red y trabajo auxiliar fuera del hilo principal.
  </Tab>

  <Tab value="Un solo hilo">
    La directiva `SINGLE_THREADED` permite una configuración donde el procesamiento se coordina desde el ciclo de actualización.
  </Tab>
</Tabs>

<Callout title="Integración con Unity" type="warn">
  No asumas que todo callback del servidor ocurre en el hilo principal de Unity. El código que acceda a objetos de Unity debe considerar explícitamente su contexto de ejecución.
</Callout>

## Paquetes personalizados [#paquetes-personalizados]

Cuando el servidor recibe un paquete que no procesa internamente puede utilizar `onCustomPacket`.

```csharp
servidor.onCustomPacket = (jugador, buffer, reader, request, reliable) =>
{
    // Interpretar una extensión de protocolo propia.
};
```

Las extensiones deberían definir claramente versión, serialización, permisos y comportamiento ante datos inválidos.

## Administración y lobby [#administración-y-lobby]

El servidor puede mantener una lista de administradores y enlazarse con un servidor de lobby mediante `enlaceLobbyLink`.

Estas funciones son servicios alrededor de la sesión, no sustitutos del modelo de canales.

## Servidor local [#servidor-local]

`ServidorJuego` puede conectarse a un `ClienteJuego` local, utilizando colas internas en lugar de sockets.

Consulta [Servidor local](/docs/red/v1/runtime/servidor-local) y [la guía correspondiente](/docs/red/v1/guias/servidor-local).

## Relación con TNet [#relación-con-tnet]

| Eco                   | TNet         | Responsabilidad   |
| --------------------- | ------------ | ----------------- |
| `ServidorJuego`       | `GameServer` | Runtime servidor  |
| `Jugador` / `Entidad` | `Player`     | Participante      |
| `Canal`               | `Channel`    | Ámbito compartido |
| `Paquete`             | `Packet`     | Protocolo         |

## Referencias [#referencias]

<Cards>
  <Card title="Servidor en profundidad" href="/docs/red/v1/runtime/servidor-en-profundidad">
    Detalles de implementación, persistencia y concurrencia.
  </Card>

  <Card title="Persistencia" href="/docs/red/v1/persistencia">
    Estado y almacenamiento del servidor.
  </Card>

  <Card title="Servidor local" href="/docs/red/v1/guias/servidor-local">
    Pruebas cliente-servidor dentro del mismo proceso.
  </Card>
</Cards>
