# Cambiar de vista (/docs/camara/v1/guias/cambiar-vista)



La API central es `CambiarVista`.

```csharp
camara.CambiarVista(vistaCombate, 0.35f);
```

Una duración de `0` hace el cambio inmediatamente. Una duración positiva conserva la posición y dirección de la vista anterior y realiza una transición hacia la nueva. fileciteturn414file0

Para una transición de juego, la decisión recomendable es separar el evento que cambia el contexto de la cámara de la propia lógica de interpolación.

```text
Entrar en combate
       ↓
Seleccionar vista combate
       ↓
CambiarVista(..., duración)
       ↓
Camara resuelve transición
```
