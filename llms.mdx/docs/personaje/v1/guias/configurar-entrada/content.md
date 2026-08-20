# Configurar la entrada (/docs/personaje/v1/guias/configurar-entrada)



La entrada de `Jugador` está separada de la resolución física mediante `ControladorEntradaJugador`.

<div className="fd-steps">
  <div className="fd-step">
    ### Preparar el controlador [#preparar-el-controlador-step]

    Asegúrate de que `Jugador` disponga de una instancia de `ControladorEntradaJugador`. La implementación actual la crea automáticamente cuando no existe.
  </div>

  <div className="fd-step">
    ### Conectar la fuente de entrada [#conectar-la-fuente-de-entrada-step]

    Haz que tu sistema de input entregue movimiento y acciones de salto a la capa de entrada, en lugar de modificar directamente el `Transform` del personaje.
  </div>

  <div className="fd-step">
    ### Configurar la cámara [#configurar-la-cámara-step]

    Cuando el movimiento debe ser relativo a la cámara, asigna `Jugador.trCamara`. El controlador utilizará esa referencia para calcular la dirección.
  </div>

  <div className="fd-step">
    ### Validar el resultado [#validar-el-resultado-step]

    Comprueba que el input sólo expresa intención y que `Jugador` sigue siendo responsable de velocidad, gravedad, salto, pendientes y colisiones.

    <Callout title="Separación recomendada" type="info">
      La entrada decide qué quiere hacer el jugador. `Jugador` decide cómo convertir esa intención en movimiento físico.
    </Callout>
  </div>
</div>
