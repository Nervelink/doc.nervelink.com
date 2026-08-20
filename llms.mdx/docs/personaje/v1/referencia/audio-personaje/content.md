# AudioPersonaje (/docs/personaje/v1/referencia/audio-personaje)



`AudioPersonaje` traduce el estado del movimiento en señales de audio.

## Integración [#integración]

`Jugador` crea y mantiene una instancia de `AudioPersonaje` junto con `AnimacionPersonaje` y `Locomocion`.

## Responsabilidad [#responsabilidad]

Debe encargarse de la presentación sonora, no de las reglas de movimiento. Los eventos del controlador determinan cuándo reproducir sonidos; el audio no decide el estado físico.
