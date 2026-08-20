# Preguntas frecuentes (/docs/misiones/v1/preguntas-frecuentes)



### ¿Misiones ya ejecuta objetivos y ramas? [#misiones-ya-ejecuta-objetivos-y-ramas]

No. El manifiesto describe misión principal, secundarias, ramificaciones y distintos resultados, pero el código actual disponible sólo implementa la infraestructura de datos `DatosMisiones`. fileciteturn451file0 fileciteturn456file0

### ¿Dónde están los datos? [#dónde-están-los-datos]

En el editor, `DatosMisiones` declara `Assets/Pandora/Datos/Misiones/Resources` como ruta de datos predeterminados. En ejecución busca `Datos/DatosMisiones` mediante `Resources.Load`. fileciteturn453file0

### ¿Qué diferencia hay entre `DatosMisiones.I` y `DatosMisiones.Cargar()`? [#qué-diferencia-hay-entre-datosmisionesi-y-datosmisionescargar]

`I` proporciona la instancia almacenada y cacheada en la propiedad estática. `Cargar()` delega en `IDatos.Cargar<DatosMisiones>()` para realizar una carga independiente según el sistema genérico de datos de Motor. fileciteturn453file0

### ¿Qué dependencia necesita? [#qué-dependencia-necesita]

El paquete declara `Motor` 1.1.4.0 como dependencia. fileciteturn451file0
