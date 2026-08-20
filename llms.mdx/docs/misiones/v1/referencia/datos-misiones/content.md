# DatosMisiones (/docs/misiones/v1/referencia/datos-misiones)



`DatosMisiones` pertenece al espacio `Pandora.Misiones` y hereda de `IDatos` proporcionado por `Pandora.Motor`. fileciteturn453file0

## API disponible [#api-disponible]

| Miembro    | Tipo                   | Función                                                             |
| ---------- | ---------------------- | ------------------------------------------------------------------- |
| `I`        | `DatosMisiones`        | Obtiene la instancia cargada desde `Resources/Datos/DatosMisiones`. |
| `Cargar()` | `static DatosMisiones` | Solicita una copia mediante `IDatos.Cargar<DatosMisiones>()`.       |

## Instancia global [#instancia-global]

```csharp
DatosMisiones datos = DatosMisiones.I;
```

La propiedad utiliza una caché estática y, si todavía no existe, carga el recurso mediante `Resources.Load`. fileciteturn453file0

## Carga independiente [#carga-independiente]

```csharp
DatosMisiones datos = DatosMisiones.Cargar();
```

Este método delega en el sistema genérico de `IDatos`. fileciteturn453file0

## Inicialización de editor [#inicialización-de-editor]

En el editor, el módulo registra `IDatos.Inicializar<DatosMisiones>()` y declara como ruta predeterminada:

```text
Assets/Pandora/Datos/Misiones/Resources
```

Esto forma parte del tooling de datos; no es una operación que el gameplay deba repetir manualmente. fileciteturn453file0
