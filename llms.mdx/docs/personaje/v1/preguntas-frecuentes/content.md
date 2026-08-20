# Preguntas frecuentes (/docs/personaje/v1/preguntas-frecuentes)



## ¿Personaje y Jugador son lo mismo? [#personaje-y-jugador-son-lo-mismo]

No. `Personaje` implementa un controlador orientado a rutas y movimiento, mientras que `Jugador` añade entrada humana, salto, control aéreo y estados propios del control directo.

## ¿Necesito un sistema de pathfinding? [#necesito-un-sistema-de-pathfinding]

No para recorrer una ruta de puntos. Personaje utiliza `PuntoRuta` y la lista `camino`. Si necesitas navegación dinámica, debes integrar un sistema especializado y utilizar Personaje como capa de locomoción.

## ¿Dónde se aplica la gravedad? [#dónde-se-aplica-la-gravedad]

El controlador calcula la velocidad vertical y delega la aplicación de la velocidad física en `Locomocion`.

## ¿Quién controla la animación? [#quién-controla-la-animación]

`AnimacionPersonaje` traduce el estado y movimiento del controlador a parámetros del `Animator`. La lógica de movimiento no debe depender directamente de clips concretos.

## ¿Puedo usar Personaje para un NPC? [#puedo-usar-personaje-para-un-npc]

Sí. `Personaje` está pensado para movimiento por ruta y puede funcionar como base para personajes no controlados directamente por el jugador.

## ¿Jugador depende de entrada? [#jugador-depende-de-entrada]

Sí. `Jugador` utiliza `ControladorEntradaJugador` para obtener la intención de movimiento y salto.

## ¿El suavizado es networking? [#el-suavizado-es-networking]

No. `SuavizadoPosicion` y `SuavizadoRotacion` sirven para transiciones visuales. Pueden utilizarse junto a un sistema de red, pero no sustituyen la sincronización.
