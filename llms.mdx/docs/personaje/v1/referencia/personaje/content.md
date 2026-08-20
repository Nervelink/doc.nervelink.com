# Personaje (/docs/personaje/v1/referencia/personaje)



`Personaje` implementa `IControlador` y calcula el movimiento del personaje a partir de una posición objetivo y una ruta de `PuntoRuta`.

## Propiedades principales [#propiedades-principales]

| Propiedad                  | Propósito                                           |
| -------------------------- | --------------------------------------------------- |
| `velMovimiento`            | Velocidad general de desplazamiento                 |
| `gravedad`                 | Aceleración vertical cuando está en el aire         |
| `tiempoCancelarMovimiento` | Ventana para detectar falta de desplazamiento       |
| `distanciaMinima`          | Distancia mínima para considerar alcanzado un punto |
| `camino`                   | Lista de `PuntoRuta`                                |
| `modelo`                   | Modelo visual del personaje                         |

## API principal [#api-principal]

`ObtenerLocomocion()` devuelve la capa de movimiento físico.

`ObtenerAnimPersonaje()` devuelve la integración con `AnimacionPersonaje`.

`ObtenerVelocidadMovimiento()` devuelve la velocidad de desplazamiento calculada.

`ObtenerVelocidad()` devuelve la velocidad resultante incluyendo el componente vertical.

`Superficie()` indica si el controlador considera que está sobre una superficie.

## Ciclo [#ciclo]

`Update` actualiza animación y ruta. `FixedUpdate` resuelve suelo, tiempo, movimiento y gravedad. `LateUpdate` actualiza el sensor.
