# Primer cliente (/docs/red/v1/guias/primer-cliente)



# Primer cliente [#primer-cliente]

Esta guía muestra el recorrido mínimo para conectar un cliente de Eco a un servidor y comprobar que la sesión está operativa.

<Callout title="Antes de empezar" type="info">
  Esta guía asume que ya conoces la estructura básica de Eco y que el proyecto incluye las partes de cliente necesarias. Para entender la arquitectura antes de escribir código, consulta [Fundamentos](/docs/red/v1/fundamentos).
</Callout>

## 1. Obtener el cliente [#1-obtener-el-cliente]

La implementación de Eco concentra la gestión de sesión en `ClienteJuego`. La fachada que utilices desde tu proyecto debe proporcionar acceso a esa instancia según la arquitectura de Pandora.

Conceptualmente:

```text
Juego
  ↓
Eco
  ↓
ClienteJuego
```

## 2. Conectar [#2-conectar]

El cliente puede conectarse a un servidor remoto mediante su endpoint de red.

```csharp
cliente.Connect(endpoint);
```

En este punto comienza el ciclo de conexión y handshake. No asumas que la conexión está lista inmediatamente después de llamar a `Connect`.

## 3. Esperar el estado conectado [#3-esperar-el-estado-conectado]

Comprueba el estado de la sesión antes de realizar operaciones que dependan del servidor.

```csharp
if (cliente.isConnected)
{
    // La sesión está disponible.
}
```

El cliente también mantiene estados separados para el intento de conexión y la entrada a canales.

```text
Intentando conectar
        ↓
     Handshake
        ↓
    Conectado
        ↓
   Sin canal todavía
        ↓
   Canal conectado
```

## 4. Entrar en un canal [#4-entrar-en-un-canal]

Una vez establecida la conexión, solicita la entrada en un canal.

```csharp
cliente.JoinChannel(
    channelID: 1,
    levelName: "Game",
    persistent: false,
    playerLimit: 4,
    password: ""
);
```

La operación es asíncrona desde el punto de vista del cliente. Eco mantiene la solicitud como pendiente hasta recibir la respuesta del servidor.

Consulta [Canales](/docs/red/v1/modelo/canales) para conocer el modelo multi-canal y las restricciones de entrada y salida.

## 5. Comprobar el canal [#5-comprobar-el-canal]

Cuando la respuesta de unión ha sido procesada puedes consultar la pertenencia:

```csharp
if (cliente.IsInChannel(1))
{
    Canal canal = cliente.GetChannel(1);
}
```

No utilices la existencia de una solicitud de unión como si significara que el cliente ya está dentro del canal.

## 6. Comprobar la comunicación [#6-comprobar-la-comunicación]

A partir de este punto puedes crear objetos, enviar RFC o modificar datos de estado según las necesidades del juego.

La separación recomendada es:

```text
Acción puntual       → RFC
Estado de una entidad → datos / sincronización
Datos binarios       → Buffer
Transporte            → TCP / UDP
```

## 7. Desconectar correctamente [#7-desconectar-correctamente]

Cuando el juego termina la sesión, utiliza el mecanismo de desconexión de Eco para liberar la sesión y los recursos de transporte.

```csharp
cliente.Disconnect();
```

No es necesario abandonar manualmente cada canal para poder cerrar la conexión; la desconexión gestiona el estado de la sesión.

## Errores frecuentes [#errores-frecuentes]

### Intentar enviar antes de estar conectado [#intentar-enviar-antes-de-estar-conectado]

Un objeto puede existir localmente antes de que el cliente tenga un canal válido y una conexión utilizable. Comprueba siempre el estado antes de depender de una operación de red.

### Confundir `Connect` con "ya estoy dentro de una partida" [#confundir-connect-con-ya-estoy-dentro-de-una-partida]

Conectar establece la sesión con el servidor. Entrar en un canal es una operación distinta.

### Suponer que sólo existe un canal [#suponer-que-sólo-existe-un-canal]

Eco permite mantener varios canales simultáneamente. No diseñes tu gestión de sesión como si `cliente.channels[0]` fuera necesariamente el único contexto de juego.

## Siguiente paso [#siguiente-paso]

Continúa con [Primer objeto](/docs/red/v1/guias/primer-objeto) para pasar de una conexión funcional a una entidad de red.

## Referencias [#referencias]

<Card title="Cliente" href="/docs/red/v1/runtime/cliente">
  Referencia conceptual y de ciclo de vida de `ClienteJuego`.
</Card>

<Card title="Canales" href="/docs/red/v1/modelo/canales">
  Cómo se gestiona la pertenencia a canales.
</Card>

<Card title="Eco en GitHub" href="https://github.com/Nervelink/eco">
  Código fuente de la implementación actual.
</Card>
