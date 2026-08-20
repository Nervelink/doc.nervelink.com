# Configurar una cámara (/docs/camara/v1/guias/configurar-camara)



Añade `Camara` al mismo `GameObject` que la `Camera` de Unity.

<Steps>
  <Step>
    Asigna una 

    `VistaCamara`

     inicial si quieres que la cámara arranque vinculada a una vista.
  </Step>

  <Step>
    Elige 

    `Update`

     o 

    `LateUpdate`

     en 

    `tipoActualizador`

     según el orden que necesite el resto del juego.
  </Step>

  <Step>
    Selecciona el tipo de suavizado y ajusta velocidad de posición y rotación.
  </Step>

  <Step>
    Configura proyección y propiedades desde la vista activa.
  </Step>

  <Step>
    Activa 

    `previsualizar`

     sólo mientras estés ajustando la cámara en el editor.
  </Step>
</Steps>

`Camara` está marcado con `[ExecuteAlways]`, por lo que parte de su comportamiento también puede ejecutarse fuera del modo Play. fileciteturn414file0
