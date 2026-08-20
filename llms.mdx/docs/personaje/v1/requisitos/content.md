# Requisitos (/docs/personaje/v1/requisitos)



## Dependencias [#dependencias]

El paquete `com.pandora.modulo.personaje` declara actualmente estas dependencias:

| Paquete                     | Versión mínima declarada |
| --------------------------- | ------------------------ |
| `com.pandora.modulo.motor`  | `1.1.4.0`                |
| `com.pandora.modulo.nucleo` | `1.3.6.1`                |

La versión registrada del módulo Personaje es `1.4.8.2`.

## Entorno [#entorno]

El repositorio está organizado como un proyecto Unity y contiene escenas, recursos, lógica y configuración de paquetes. El README histórico declara Unity 2019.4+ y .NET 4.x; esas cifras deben tratarse como información histórica hasta fijar la versión del proyecto consumidor.

## Requisitos de una entidad [#requisitos-de-una-entidad]

Un personaje necesita una jerarquía de GameObjects coherente con el controlador. Cuando se utiliza la integración de animación, debe existir un `Animator` disponible para `AnimacionPersonaje`.

`Jugador` añade además `ControladorEntradaJugador`, `DetectorTecho`, `AnimacionPersonaje`, `AudioPersonaje` y `Locomocion` como partes de su composición de runtime.

## Limitaciones [#limitaciones]

`Personaje` proporciona locomoción y recorrido por puntos, no un sistema de navegación general. Una ruta se representa mediante `PuntoRuta` y la colección `camino`.

No debe confundirse el suavizado de posición/rotación con un sistema de movimiento autoritativo o de networking: son herramientas de presentación y transición visual.
