# ConfiguracionDifuminado (/docs/camara/v1/referencia/configuracion-difuminado)



`ConfiguracionDifuminado` representa la configuración que recibe el algoritmo seleccionado por `Camara`.

La arquitectura permite que el componente de cámara no dependa de parámetros concretos de cada algoritmo: `Camara` selecciona una estrategia y entrega la configuración a `IAlgoritmoDifuminado`. fileciteturn419file0

Para la implementación actual, la estrategia disponible es `DifuminadoEscalable`.
