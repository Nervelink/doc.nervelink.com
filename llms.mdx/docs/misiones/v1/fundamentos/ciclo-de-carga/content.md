# Ciclo de carga (/docs/misiones/v1/fundamentos/ciclo-de-carga)



El módulo utiliza dos rutas de acceso principales.

<Steps>
  <Step>
    En el editor, `DatosMisiones` se inicializa mediante `IDatos.Inicializar<DatosMisiones>()`.
  </Step>

  <Step>
    La ruta predeterminada de datos se define como `Assets/Pandora/Datos/Misiones/Resources`.
  </Step>

  <Step>
    En ejecución, `DatosMisiones.I` busca `Datos/DatosMisiones` mediante `Resources.Load`.
  </Step>

  <Step>
    Cuando se necesita una carga independiente, `DatosMisiones.Cargar()` delega en `IDatos.Cargar<DatosMisiones>()`.
  </Step>
</Steps>

La ruta del editor y la ruta usada por `Resources.Load` cumplen funciones diferentes: la primera localiza los datos predeterminados del paquete; la segunda identifica el recurso que Unity puede cargar en ejecución. fileciteturn453file0
