# Vistas (/docs/camara/v1/fundamentos/vistas)



Una `VistaCamara` representa una configuración lógica de cámara. `Camara` la activa y consulta para obtener posición, dirección y propiedades de proyección.

El flujo conceptual es:

```text
VistaCamara
   ↓
ActivarVista()
   ↓
ObtenerPosicion()
ObtenerDireccion()
   ↓
Camara
```

Una vista puede representar diferentes contextos del juego: cámara normal, minimapa, combate u otros estilos. El módulo se diseñó precisamente alrededor de esta separación de estilos. fileciteturn411file0

La ventaja es que cambiar de vista no obliga a reemplazar el componente `Camara`; se cambia el proveedor de posición, dirección y propiedades.
