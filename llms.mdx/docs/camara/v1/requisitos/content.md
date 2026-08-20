# Requisitos (/docs/camara/v1/requisitos)



## Dependencias [#dependencias]

El paquete `com.pandora.modulo.camara` declara actualmente:

| Módulo | Versión mínima declarada |
| ------ | ------------------------ |
| Motor  | 1.2.7.5                  |
| Núcleo | 1.3.7.4                  |
| Cámara | 1.1.2.11                 |

fileciteturn411file0

## Requisitos de integración [#requisitos-de-integración]

* El componente `Camara` trabaja junto a una `Camera` de Unity en el mismo `GameObject`.
* El módulo contiene código de editor y ejecución continua (`[ExecuteAlways]`).
* El sistema de difuminado requiere recursos de render adicionales y debe configurarse según el presupuesto gráfico.

## Limitaciones a tener en cuenta [#limitaciones-a-tener-en-cuenta]

El difuminado puede aumentar coste de render y memoria. `Resolucion`, `RegionDifuminada` y `actualizacionMax` existen precisamente para limitar ese coste. fileciteturn419file0
