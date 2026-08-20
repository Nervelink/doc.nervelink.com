# Difuminado (/docs/camara/v1/fundamentos/difuminado)



El módulo genera una `RenderTexture` auxiliar y ejecuta un algoritmo de difuminado para ocultar o suavizar elementos de la escena cuando interfieren con la vista. `Camara` expone la textura mediante `VentanaDifuminada` y controla cuándo necesita actualizarse. fileciteturn414file0

El flujo es:

```text
Camera
  ↓
RenderTexture
  ↓
IAlgoritmoDifuminado
  ↓
VentanaDifuminada
```

La implementación actual utiliza `AlgoritmoDifuminado.DifuminadoEscalable`, respaldado por `AlgoritmoEscalable`, y dispone de configuración específica para el algoritmo. fileciteturn418file0

## Coste [#coste]

El difuminado implica renderizado adicional. `Resolucion`, `RegionDifuminada` y `actualizacionMax` permiten limitar coste de memoria y frecuencia de actualización. No conviene ejecutarlo a máxima resolución y máxima frecuencia cuando la escena no lo necesita.
