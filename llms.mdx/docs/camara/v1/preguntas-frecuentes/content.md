# Preguntas frecuentes (/docs/camara/v1/preguntas-frecuentes)



### ¿Puedo cambiar de cámara sin reemplazar el componente? [#puedo-cambiar-de-cámara-sin-reemplazar-el-componente]

Sí. La arquitectura está pensada alrededor de una `VistaCamara` activa. `CambiarVista` permite sustituirla manteniendo el mismo controlador.

### ¿Qué diferencia hay entre transición y suavizado? [#qué-diferencia-hay-entre-transición-y-suavizado]

La transición describe el cambio entre dos vistas durante un intervalo. El suavizado controla cómo la cámara sigue o alcanza su objetivo una vez resuelta la vista.

### ¿Qué hace `LateUpdate`? [#qué-hace-lateupdate]

Permite actualizar la cámara después del `Update`, útil cuando otros sistemas modifican objetivos o transforms durante `Update`.

### ¿El difuminado es gratuito? [#el-difuminado-es-gratuito]

No. Usa una `RenderTexture` auxiliar y procesamiento adicional. La resolución, región y frecuencia de actualización deben ajustarse al presupuesto gráfico. fileciteturn419file0

### ¿Puedo usar Cámara para un minimapa o combate? [#puedo-usar-cámara-para-un-minimapa-o-combate]

Sí. El manifiesto del módulo contempla distintos estilos de cámara, incluyendo cámara normal, minimapa y combate. fileciteturn411file0
