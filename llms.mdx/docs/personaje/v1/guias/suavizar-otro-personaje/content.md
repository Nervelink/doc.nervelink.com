# Suavizar otro personaje (/docs/personaje/v1/guias/suavizar-otro-personaje)



`SuavizadoPosicion` y `SuavizadoRotacion` son útiles cuando un personaje recibe cambios de posición u orientación desde una fuente externa.

<div className="fd-steps">
  <div className="fd-step">
    ### Separar el estado lógico [#separar-el-estado-lógico-step]

    Mantén la posición y rotación reales en la capa que posee la autoridad del movimiento.
  </div>

  <div className="fd-step">
    ### Aplicar el suavizado [#aplicar-el-suavizado-step]

    Utiliza `SuavizadoPosicion` y `SuavizadoRotacion` sobre la representación visual para amortiguar los cambios.
  </div>

  <div className="fd-step">
    ### Ajustar la respuesta [#ajustar-la-respuesta-step]

    Busca un equilibrio entre respuesta inmediata y continuidad visual. Un suavizado excesivo introduce sensación de retraso.
  </div>

  <div className="fd-step">
    ### Usarlo con red [#usarlo-con-red-step]

    Cuando el estado procede de Eco, el valor sincronizado sigue siendo la referencia lógica y el suavizado sólo modifica la presentación local.

    <Callout title="No confundir conceptos" type="warn">
      Suavizar una transformación no corrige por sí mismo latencia, predicción, reconciliación ni autoridad de red.
    </Callout>
  </div>
</div>
