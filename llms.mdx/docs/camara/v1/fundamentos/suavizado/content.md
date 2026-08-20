# Suavizado (/docs/camara/v1/fundamentos/suavizado)



`Camara` admite dos estrategias de suavizado para la posición: `Lineal` y `Progresivo`. La rotación utiliza interpolación esférica. fileciteturn414file0

| Tipo         | Uso                                  |
| ------------ | ------------------------------------ |
| `Lineal`     | Interpolación basada en velocidad    |
| `Progresivo` | `SmoothDamp` con tiempo de respuesta |

Además, la cámara puede extrapolar posición y rotación cuando la configuración lo requiere. Estas opciones deben utilizarse con criterio: la extrapolación puede hacer que la cámara anticipe un objetivo que todavía no se ha confirmado.

`velLineal`, `tiempoProgresivo` y `velSuavizado` controlan respectivamente la respuesta de posición lineal, posición progresiva y rotación.
