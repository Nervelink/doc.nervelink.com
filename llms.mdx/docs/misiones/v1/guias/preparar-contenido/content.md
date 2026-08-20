# Preparar contenido (/docs/misiones/v1/guias/preparar-contenido)



El repositorio actual no contiene todavía un editor de misiones ni clases de definición de objetivos o ramas. Por ello, esta guía establece la responsabilidad de la base de datos sin inventar un workflow de autoría que todavía no existe.

El paquete reserva la ruta `Assets/Pandora/Datos/Misiones/` para los datos y la ruta `Assets/Pandora/Logica/Misiones/` para la lógica. fileciteturn451file0

<Callout title="Diseño previsto" type="info">
  El manifiesto declara soporte para misión principal, secundarias, ramificaciones y distintos resultados. Esas capacidades deben considerarse diseño previsto hasta que aparezca su implementación en el módulo.
</Callout>

La recomendación es mantener las definiciones de misión como datos y separar posteriormente la evaluación de objetivos, el estado de la misión y las recompensas en sistemas de runtime.
