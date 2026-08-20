# Configurar un jugador (/docs/personaje/v1/guias/configurar-jugador)



<div className="fd-steps">
  <div className="fd-step">
    ### Preparar el objeto [#preparar-el-objeto-step]

    Usa una jerarquía con el modelo visual y un `Animator` si vas a utilizar animación automática.
  </div>

  <div className="fd-step">
    ### Usar `Jugador` [#usar-jugador-step]

    Configura `velMovimiento`, `velSalto`, `controlAire`, `friccionSuelo`, `friccionAire`, `gravedad` y `limitePendiente` según la sensación de movimiento que busques.
  </div>

  <div className="fd-step">
    ### Configurar entrada [#configurar-entrada-step]

    Deja que `ControladorEntradaJugador` sea la capa que traduzca el dispositivo de entrada a intención de movimiento y salto.
  </div>

  <div className="fd-step">
    ### Configurar cámara [#configurar-cámara-step]

    Asigna `trCamara` para que la dirección del movimiento pueda calcularse respecto a la orientación de la cámara.
  </div>

  <div className="fd-step">
    ### Probar estados [#probar-estados-step]

    Comprueba suelo, salto, caída, pendiente y golpe de techo. Estos estados alimentan tanto la locomoción como la presentación.
  </div>
</div>
