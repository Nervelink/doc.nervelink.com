# Transiciones (/docs/camara/v1/fundamentos/transiciones)



`CambiarVista(VistaCamara vista, float duracion)` permite cambiar la vista de forma instantánea o mediante una transición temporal. La clase guarda la posición y dirección iniciales y calcula la interpolación hasta el objetivo. fileciteturn414file0

```text
Vista A
  ↓
posición / dirección inicial
  ↓
transición
  ↓
Vista B
```

Una duración de `0` aplica el cambio directamente. Una duración positiva utiliza interpolación de posición y rotación.

La transición es independiente del suavizado posterior: primero se resuelve el cambio de vista y después la cámara puede seguir suavizando posición o rotación según la configuración.
