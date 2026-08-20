# Depurar una cámara (/docs/camara/v1/guias/depurar-camara)



Cuando una cámara no responde como esperas, comprueba primero la vista activa y después el ciclo de actualización.

```text
¿Hay VistaCamara?
   ↓ sí
¿Está activa?
   ↓ sí
¿Update o LateUpdate?
   ↓
¿La posición cambia?
   ↓
¿El suavizado está alterando el resultado?
   ↓
¿El problema aparece sólo con difuminado?
```

`previsualizar` permite visualizar la región del difuminado y modificarla desde el editor. `DebugCamaraExtension` y las utilidades del módulo están diseñadas para facilitar inspección y diagnóstico. fileciteturn413file0
