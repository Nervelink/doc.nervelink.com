# Controladores (/docs/personaje/v1/fundamentos/controladores)



## Personaje [#personaje]

`Personaje` está orientado a personajes que reciben posiciones objetivo mediante `camino`, representado por una lista de `PuntoRuta`.

Su responsabilidad principal es calcular el vector hacia el punto actual, aplicar velocidad de movimiento y procesar gravedad.

## Jugador [#jugador]

`Jugador` añade control directo mediante `ControladorEntradaJugador`. Además incorpora salto, control aéreo, fricción, pendientes y estados como suelo, deslizamiento, ascenso, descenso y salto.

## Comparación [#comparación]

| Característica     | Personaje                     | Jugador                      |
| ------------------ | ----------------------------- | ---------------------------- |
| Ruta por puntos    | Sí                            | No es su mecanismo principal |
| Entrada humana     | No                            | Sí                           |
| Salto              | No es el foco                 | Sí                           |
| Control aéreo      | Básico/no específico          | Sí                           |
| Cámara relativa    | No                            | Sí, mediante `trCamara`      |
| Detección de techo | No                            | Sí                           |
| Animación          | Sí                            | Sí                           |
| Audio              | Puede reutilizar presentación | Sí, integrado en `Jugador`   |

## Elección recomendada [#elección-recomendada]

Usa `Jugador` para el personaje controlado directamente por el usuario. Usa `Personaje` para actores que siguen rutas o para una base sencilla de movimiento que después sea especializada por otro controlador.
