# Arquitectura (/docs/personaje/v1/fundamentos/arquitectura)



El módulo está organizado por responsabilidades. El controlador calcula la intención y el estado de movimiento; `Locomocion` encapsula el movimiento físico; los sensores proporcionan información del entorno; animación y audio presentan el resultado.

```text
Entrada / Ruta
      ↓
Controlador
      ↓
Locomocion ← Sensores
      ↓
Transform
      ↓
Animación / Audio
```

## Capas principales [#capas-principales]

| Capa                 | Responsabilidad                                   |
| -------------------- | ------------------------------------------------- |
| `Jugador`            | Control humano, salto, aire y estados del jugador |
| `Personaje`          | Movimiento por ruta y gravedad                    |
| `Locomocion`         | Aplicación y ajuste del movimiento físico         |
| `Sensor`             | Detección de suelo, superficies y entorno         |
| `DetectorTecho`      | Detección específica de techos para el jugador    |
| `AnimacionPersonaje` | Traducción de estado a Animator                   |
| `AudioPersonaje`     | Pasos, salto y eventos de movimiento              |
| `SuavizadoPosicion`  | Transición visual de posición                     |
| `SuavizadoRotacion`  | Transición visual de rotación                     |

## Ciclo de actualización [#ciclo-de-actualización]

`Jugador` y `Personaje` separan trabajo entre `Update`, `FixedUpdate` y `LateUpdate`. La entrada y presentación se actualizan en el ciclo de frame; la resolución del movimiento y gravedad utiliza `FixedUpdate`; los sensores se actualizan después del movimiento cuando corresponde.

## Por qué está separado [#por-qué-está-separado]

La separación permite sustituir una fuente de movimiento sin reescribir la presentación. Un NPC puede utilizar una ruta mientras un jugador utiliza entrada humana y ambos reutilizan la misma locomoción, sensores y animación.
