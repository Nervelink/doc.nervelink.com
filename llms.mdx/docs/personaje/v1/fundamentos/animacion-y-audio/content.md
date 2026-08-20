# Animación y audio (/docs/personaje/v1/fundamentos/animacion-y-audio)



`AnimacionPersonaje` y `AudioPersonaje` son capas de presentación. El controlador no necesita conocer los clips concretos para resolver su movimiento.

## Animación [#animación]

`AnimacionPersonaje` recibe referencias del controlador y del `Animator` y actualiza sus variables críticas desde el estado de movimiento.

```text
Jugador / Personaje
       ↓
estado + velocidad
       ↓
AnimacionPersonaje
       ↓
Animator
```

## Audio [#audio]

`AudioPersonaje` utiliza el movimiento, locomoción y eventos del personaje para reproducir pasos, salto y otros efectos.

## Recomendación [#recomendación]

Mantén las reglas de gameplay fuera de estas capas. Una animación puede cambiar sin alterar la forma en que `Jugador` calcula su velocidad.

## Eventos [#eventos]

El controlador puede notificar eventos como el aterrizaje. La presentación puede reaccionar a ellos para reproducir sonido o modificar animaciones sin convertirlos en parte del cálculo de locomoción.
