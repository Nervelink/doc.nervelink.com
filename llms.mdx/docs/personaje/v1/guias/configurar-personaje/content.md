# Configurar un personaje (/docs/personaje/v1/guias/configurar-personaje)



<div className="fd-steps">
  <div className="fd-step">
    ### Preparar el GameObject [#preparar-el-gameobject-step]

    Crea el objeto raíz del personaje y coloca el modelo visual en la jerarquía esperada.
  </div>

  <div className="fd-step">
    ### Añadir el controlador [#añadir-el-controlador-step]

    Configura `Personaje` como controlador de movimiento. Revisa `velMovimiento`, `gravedad`, `tiempoCancelarMovimiento` y `distanciaMinima`.
  </div>

  <div className="fd-step">
    ### Preparar locomoción y sensores [#preparar-locomoción-y-sensores-step]

    Asegúrate de que `Locomocion` pueda inicializarse y que el objeto tenga la geometría necesaria para detectar suelo y superficies.
  </div>

  <div className="fd-step">
    ### Crear una ruta [#crear-una-ruta-step]

    Añade `PuntoRuta` y asígnalos a `camino`. El controlador utiliza el índice actual para seleccionar el objetivo.
  </div>

  <div className="fd-step">
    ### Probar el recorrido [#probar-el-recorrido-step]

    Ejecuta el personaje y verifica que avance entre puntos, aplique gravedad y vuelva al primer punto cuando complete la lista.

    <Callout title="Importante" type="warn">
      `Personaje` recorre puntos. Si necesitas generar rutas dinámicamente o evitar obstáculos, intégralo con un sistema de navegación y utiliza Personaje como capa de locomoción.
    </Callout>
  </div>
</div>
