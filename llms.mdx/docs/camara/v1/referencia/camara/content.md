# Camara (/docs/camara/v1/referencia/camara)



`Pandora.Camara.Camara` es el componente central del módulo. Está marcado con `[ExecuteAlways]` y mantiene la vista activa, la actualización, el suavizado y el sistema de difuminado. fileciteturn419file0

## Propiedades principales [#propiedades-principales]

| Propiedad          | Función                                     |
| ------------------ | ------------------------------------------- |
| `vista`            | Vista activa                                |
| `tipoActualizador` | `Update` o `LateUpdate`                     |
| `tipoSuavizado`    | `Lineal` o `Progresivo`                     |
| `velLineal`        | Velocidad de suavizado lineal               |
| `tiempoProgresivo` | Tiempo de `SmoothDamp`                      |
| `velSuavizado`     | Velocidad de rotación                       |
| `extrapolarPos`    | Extrapolación de posición                   |
| `extrapolarRot`    | Extrapolación de rotación                   |
| `actualizacionMax` | Límite de actualización del difuminado      |
| `previsualizar`    | Previsualización de la región de difuminado |

## API [#api]

```csharp
void CambiarVista(VistaCamara vista, float duracion = 0f)
void CambiarVista(GameObject go)
void ResetearPosActual()
void ResetearRotActual()
bool ActualizacionDifuminadoNecesario()
```

`VentanaDifuminada`, `Resolucion`, `RegionDifuminada` y `RegionNormalizada` exponen el estado de render auxiliar y su región de procesamiento. fileciteturn419file0
