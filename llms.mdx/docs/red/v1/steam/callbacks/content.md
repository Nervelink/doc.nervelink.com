# Callbacks (/docs/red/v1/steam/callbacks)



Steamworks es un sistema basado en callbacks. Eco encapsula su procesamiento dentro del sistema `Steam`.

## Procesamiento [#procesamiento]

La integración ejecuta `SteamAPI.RunCallbacks()` periódicamente. Cuando hay actividad de servidor o conexión P2P puede procesarlos con mayor frecuencia para reducir el retraso entre la recepción de información y su tratamiento. fileciteturn190file0

## Eventos relevantes [#eventos-relevantes]

La implementación actual registra callbacks relacionados con:

* solicitudes de sesión P2P;
* fallos de conexión P2P;
* solicitudes de unión mediante Rich Presence.

Estos callbacks son infraestructura. El gameplay debería reaccionar a los estados de Eco resultantes, no acoplar cada sistema del juego directamente a Steamworks.

<Callout title="Separación" type="info">
  Steam callback → integración Steam → Eco → sistema de juego.

  Evita Steam callback → Torre, Steam callback → UI, Steam callback → combate, etc.
</Callout>
