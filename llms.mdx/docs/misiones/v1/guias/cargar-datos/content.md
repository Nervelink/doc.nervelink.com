# Cargar los datos (/docs/misiones/v1/guias/cargar-datos)



Para acceso global, `DatosMisiones.I` carga el recurso desde `Resources/Datos/DatosMisiones`. Para obtener una copia mediante la infraestructura de `Pandora.Motor`, utiliza `DatosMisiones.Cargar()`. fileciteturn453file0

```csharp
DatosMisiones datos = DatosMisiones.I;
```

```csharp
DatosMisiones copia = DatosMisiones.Cargar();
```

<Callout title="Cuándo utilizar cada acceso" type="info">
  `I` es apropiado cuando necesitas la instancia de datos disponible como recurso. `Cargar()` delega en `IDatos` y es la opción adecuada cuando tu flujo necesita una carga independiente.
</Callout>
