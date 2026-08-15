# Primer cliente (/docs/red/v1/guias/primer-cliente)



# Primer cliente [#primer-cliente]

Esta guía cubre el recorrido mínimo desde una instancia de juego sin sesión hasta un cliente conectado y dentro de un canal.

<Callout title="Objetivo" type="info">
  Al terminar tendrás una secuencia fiable: conexión → handshake → canal → comprobación de estado. No añadiremos objetos ni gameplay hasta que este flujo sea estable.
</Callout>

## Flujo [#flujo]

```text
Aplicación
   ↓
Eco
   ↓
ClienteJuego
   ↓
Handshake
   ↓
Canal
   ↓
Sesión lista
```

## Preparación [#preparación]

La lógica de cliente se concentra en `ClienteJuego`. La fachada de Pandora puede exponerlo de otra forma, pero el modelo interno sigue teniendo estados independientes para conexión y pertenencia a canales.

## Conectar [#conectar]

<div className="fd-steps">
  <div className="fd-step">
    ### Crear la intención de conexión \[step] [#1-crear-la-intención-de-conexión-step]

    ```csharp title="Conectar con el servidor"
    cliente.Connect(endpoint);
    ```

    `Connect` inicia el proceso; no implica que la sesión esté lista en la misma instrucción.
  </div>

  <div className="fd-step">
    ### Esperar el handshake \[step] [#2-esperar-el-handshake-step]

    Comprueba el estado de conexión antes de solicitar operaciones dependientes del servidor.

    ```csharp
    if (!cliente.isConnected)
    {
        return;
    }
    ```
  </div>

  <div className="fd-step">
    ### Solicitar un canal \[step] [#3-solicitar-un-canal-step]

    ```csharp
    cliente.JoinChannel(
        channelID: 1,
        levelName: "Game",
        persistent: false,
        playerLimit: 4,
        password: ""
    );
    ```
  </div>

  <div className="fd-step">
    ### Esperar la entrada \[step] [#4-esperar-la-entrada-step]

    Mientras Eco espera la respuesta, `IsJoiningChannel(1)` puede indicar que la operación todavía está pendiente.

    ```csharp
    if (cliente.IsJoiningChannel(1))
    {
        // Todavía no usar el canal como si estuviera listo.
    }
    ```
  </div>

  <div className="fd-step">
    ### Validar el canal \[step] [#5-validar-el-canal-step]

    ```csharp
    if (cliente.IsInChannel(1))
    {
        Canal canal = cliente.GetChannel(1);
    }
    ```
  </div>
</div>

## Cliente remoto vs local [#cliente-remoto-vs-local]

<Tabs items="['Remoto', 'Servidor local']">
  <Tab value="Remoto">
    `ClienteJuego` utiliza la conexión de red y el protocolo normal. Es el entorno que debes utilizar para validar comportamiento real entre procesos o máquinas.
  </Tab>

  <Tab value="Servidor local">
    Eco puede asociar un `ServidorJuego` local para ejecutar cliente y servidor en el mismo proceso. Es excelente para pruebas, pero no reproduce todas las condiciones de una conexión real.
  </Tab>
</Tabs>

## Estado de la sesión [#estado-de-la-sesión]

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

Estos estados son independientes. Conectar no te introduce automáticamente en una partida.

## A partir de aquí [#a-partir-de-aquí]

Una vez dentro del canal puedes:

<Cards>
  <Card title="Crear un objeto" href="/docs/red/v1/guias/primer-objeto">
    Añadir una entidad con identidad de red.
  </Card>

  <Card title="Sincronizar estado" href="/docs/red/v1/guias/sincronizar-entidad">
    Mantener datos de la entidad sincronizados.
  </Card>

  <Card title="Enviar una acción" href="/docs/red/v1/guias/enviar-accion">
    Ejecutar una RFC con destinatarios concretos.
  </Card>
</Cards>

## Errores frecuentes [#errores-frecuentes]

<Callout title="Conectar no significa estar en una partida" type="warn">
  La conexión sólo establece la sesión con el servidor. La entrada a uno o varios canales es una operación posterior.
</Callout>

<Callout title="No asumas un solo canal" type="warn">
  Eco mantiene varios canales por cliente. Evita diseñar tu estado alrededor de un único `channels[0]`.
</Callout>

## Desconexión [#desconexión]

Cuando termines la sesión:

```csharp title="Cerrar la conexión"
cliente.Disconnect();
```

No necesitas convertir la desconexión en una secuencia manual de abandono de canales; el cierre de la sesión forma parte del ciclo de vida del cliente.

## Referencias [#referencias]

<Cards>
  <Card title="ClienteJuego" href="/docs/red/v1/runtime/cliente">
    Funcionamiento del runtime de cliente.
  </Card>

  <Card title="Canales" href="/docs/red/v1/modelo/canales">
    Modelo de uno o varios canales.
  </Card>

  <Card title="Eco" href="https://github.com/Nervelink/eco">
    Código fuente actual.
  </Card>
</Cards>
