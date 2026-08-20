# Modelo de datos (/docs/misiones/v1/fundamentos/modelo-de-datos)



La pieza disponible actualmente es `DatosMisiones`, una clase de datos que hereda de `IDatos` de `Pandora.Motor`. Su responsabilidad es localizar la base de datos de misiones y proporcionar acceso a ella. fileciteturn453file0

## Acceso global [#acceso-global]

`DatosMisiones.I` mantiene una instancia cargada desde:

```text
Resources/Datos/DatosMisiones
```

La carga utiliza `Resources.Load` y devuelve el recurso `DatosMisiones` existente. fileciteturn453file0

## Carga de una copia [#carga-de-una-copia]

`DatosMisiones.Cargar()` delega en `IDatos.Cargar<DatosMisiones>()`. Esto debe entenderse como un mecanismo diferente al acceso de la propiedad `I`: la aplicación puede trabajar con una copia cargada mediante la infraestructura genérica de datos. fileciteturn453file0

<Callout title="Datos frente a estado" type="info">
  La base de datos define contenido. El estado de una misión activa —por ejemplo, si un objetivo fue completado— debe pertenecer al sistema de ejecución que consuma esos datos, no a `DatosMisiones`.
</Callout>
