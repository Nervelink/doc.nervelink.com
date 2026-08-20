# Jugador (/docs/personaje/v1/referencia/jugador)



`Jugador` implementa `IControlador` y añade una capa de control directo sobre la locomoción.

## Parámetros principales [#parámetros-principales]

| Propiedad           | Propósito                                               |
| ------------------- | ------------------------------------------------------- |
| `controlando`       | Indica si la instancia está siendo controlada           |
| `velMovimiento`     | Velocidad base                                          |
| `controlAire`       | Capacidad de modificar dirección en el aire             |
| `velSalto`          | Velocidad inicial del salto                             |
| `duracionSalto`     | Duración máxima del salto variable                      |
| `friccionAire`      | Fricción del impulso en aire                            |
| `friccionSuelo`     | Fricción aplicada en suelo                              |
| `gravedad`          | Gravedad en aire                                        |
| `gravedadPendiente` | Gravedad al deslizar                                    |
| `limitePendiente`   | Umbral angular para considerar una superficie caminable |
| `impulsoLocal`      | Interpreta el impulso en espacio local                  |
| `trCamara`          | Referencia para movimiento relativo a cámara            |

## API principal [#api-principal]

`ObtenerImpulso()` devuelve el impulso actual y lo transforma a espacio mundial cuando `impulsoLocal` está activo.

`ObtenerEntrada()` devuelve `ControladorEntradaJugador`.

`ObtenerDetector()` devuelve `DetectorTecho`.

`ObtenerAnimPersonaje()` devuelve `AnimacionPersonaje`.

`ObtenerAudioPersonaje()` devuelve `AudioPersonaje`.

`Deslizando()` indica si el estado actual corresponde a una pendiente deslizante.

`Superficie()` devuelve `true` en los estados `Suelo` o `Deslizando`.

## Estado de movimiento [#estado-de-movimiento]

La implementación utiliza `EstadoPersonaje` para distinguir suelo, deslizamiento, ascenso, descenso y salto. Esa información debe considerarse parte del contrato entre el controlador y las capas de presentación.
