# Steam y conexiones personalizadas (/docs/red/v1/avanzado/steam-y-conexiones)



# Steam y conexiones personalizadas [#steam-y-conexiones-personalizadas]

Eco separa la lógica de cliente/servidor de la implementación física de la conexión mediante `IConnection`. Esto permite integrar transportes alternativos sin reescribir el protocolo superior.

## Modelo [#modelo]

```text
Eco / ClienteJuego
        │
        ▼
    IConnection
      /     \
   TCP       Steam
```

El protocolo superior sigue trabajando con paquetes y buffers mientras cambia la capa de transporte.

## Conexión personalizada [#conexión-personalizada]

La fachada de Eco expone `ProtocoloPersonalizado`:

```csharp
Eco.ProtocoloPersonalizado = miConexion;
```

Al asignar una implementación, Eco pasa al estado de verificación y envía el handshake de identidad sobre la conexión proporcionada.

<Callout title="La conexión personalizada no cambia el protocolo" type="info">
  `IConnection` sustituye el transporte. No convierte automáticamente el protocolo de Eco en otro protocolo ni elimina las reglas de canales, objetos o sincronización.
</Callout>

## Steam [#steam]

El repositorio incluye un módulo `Steam` y referencias a paquetes standalone de Steam Networking. La integración está diseñada para transportar la comunicación de Eco utilizando la infraestructura de Steam en lugar de depender únicamente de sockets directos.

## Casos de uso [#casos-de-uso]

* juegos distribuidos mediante Steam;
* NAT traversal gestionado por Steam;
* conexiones entre usuarios sin publicar endpoints TCP directamente;
* integración del networking con una API de transporte existente.

## Fuentes [#fuentes]

<Card title="Steam" href="https://github.com/Nervelink/eco/tree/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Steam">
  Código y material de integración Steam.
</Card>

<Card title="ClienteJuego" href="/docs/red/v1/referencia/api/cliente-juego">
  Punto donde se integra `IConnection`.
</Card>
