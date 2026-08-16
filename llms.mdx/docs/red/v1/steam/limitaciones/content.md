# Limitaciones (/docs/red/v1/steam/limitaciones)



Steam añade una ruta de transporte y servicios de plataforma, pero no resuelve automáticamente los problemas de networking del juego.

## Steam no sustituye un servidor autoritativo [#steam-no-sustituye-un-servidor-autoritativo]

Puedes transportar una sesión P2P y mantener un jugador como autoridad de gameplay. La arquitectura del juego es independiente del medio de transporte.

## Steam no sustituye un lobby Eco [#steam-no-sustituye-un-lobby-eco]

Rich Presence puede facilitar descubrimiento y unión, pero el estado del lobby debe seguir viviendo en Eco si contiene reglas de partida.

## Steam no sustituye autenticación de gameplay [#steam-no-sustituye-autenticación-de-gameplay]

El Steam ID identifica al usuario de la plataforma. El servidor debe decidir qué puede hacer ese usuario dentro de la partida.

## Steam no sustituye persistencia [#steam-no-sustituye-persistencia]

Guardar una partida sigue siendo responsabilidad del juego y de sus mecanismos de persistencia.

## Plataformas [#plataformas]

La integración está condicionada por compilación y no debe asumirse disponible en plataformas donde el bloque Steam no se compile. fileciteturn190file0

<Callout title="Diseño multiplataforma" type="info">
  Diseña una abstracción de identidad/transporte para que el gameplay no dependa directamente de Steam. Steam debe ser una implementación de plataforma, no la arquitectura completa de red.
</Callout>
