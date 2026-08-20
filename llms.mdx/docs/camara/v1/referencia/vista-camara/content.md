# VistaCamara (/docs/camara/v1/referencia/vista-camara)



`VistaCamara` representa el contexto lógico que `Camara` utiliza para determinar posición, dirección y propiedades. La implementación central activa y desactiva la vista y consulta su tipo durante cada actualización. fileciteturn414file0

En términos de arquitectura, la vista debe responder a:

* posición objetivo;
* dirección objetivo;
* tipo de proyección;
* dimensiones o FOV;
* si posición, rotación y propiedades deben suavizarse.

Esto permite que el componente `Camara` permanezca estable mientras el contexto cambia.
