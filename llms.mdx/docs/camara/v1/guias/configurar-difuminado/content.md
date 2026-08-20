# Configurar el difuminado (/docs/camara/v1/guias/configurar-difuminado)



El difuminado utiliza una `RenderTexture` auxiliar y el algoritmo escalable disponible actualmente. fileciteturn414file0

Los controles principales son:

| Propiedad                 | Objetivo                                |
| ------------------------- | --------------------------------------- |
| `Algoritmo`               | Selecciona la estrategia de difuminado  |
| `ConfiguracionDifuminado` | Define los parámetros del algoritmo     |
| `Resolucion`              | Reduce el tamaño de la textura auxiliar |
| `RegionDifuminada`        | Limita la zona procesada                |
| `actualizacionMax`        | Limita la frecuencia de actualización   |

Para una cámara de gameplay, empieza con la región estrictamente necesaria y una resolución reducida. Aumenta el coste sólo cuando el resultado visual lo justifique.

<Callout title="Rendimiento" type="warn">
  El difuminado añade trabajo de renderizado. No debe tratarse como una propiedad gratuita de la cámara.
</Callout>
