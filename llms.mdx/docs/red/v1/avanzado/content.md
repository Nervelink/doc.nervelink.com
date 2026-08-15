# Funciones avanzadas (/docs/red/v1/avanzado)



# Funciones avanzadas [#funciones-avanzadas]

Esta sección reúne capacidades que no necesitas para una integración básica, pero que resultan relevantes cuando el proyecto crece o necesita integraciones específicas.

<Card title="ActualizadorRed" href="/docs/red/v1/avanzado/actualizador-red">
  Centraliza `OnStart`, `Update`, `LateUpdate`, callbacks one-shot, invocaciones y coroutines para reducir el coste de muchos `MonoBehaviour`.
</Card>

<Card title="Sincronizar Rigidbody" href="/docs/red/v1/avanzado/sincronizar-rigidbody">
  Sincronización orientada a física de Unity y separación entre propietario y observadores.
</Card>

<Card title="Runtime Code" href="/docs/red/v1/avanzado/runtime-code">
  Compilación y ejecución de código C# en runtime para casos de modding y extensibilidad.
</Card>

<Card title="Lobby" href="/docs/red/v1/avanzado/lobby">
  Descubrimiento y anuncio de servidores mediante el sistema de lobby de Eco.
</Card>

<Card title="Steam y conexiones personalizadas" href="/docs/red/v1/avanzado/steam-y-conexiones">
  Integración con Steam Networking y sustitución de sockets mediante `IConnection`.
</Card>

<Card title="UPnP y NAT" href="/docs/red/v1/avanzado/upnp">
  Herramientas para descubrimiento de direcciones y apertura de puertos en routers compatibles.
</Card>

<Callout title="No añadas una capa avanzada por defecto" type="info">
  Estas herramientas existen para resolver necesidades concretas. Empieza por la API normal de `Eco` y añade estas capacidades cuando el problema que intentas resolver realmente las necesite.
</Callout>
