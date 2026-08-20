# Crear una ruta (/docs/personaje/v1/guias/crear-ruta)



Una ruta de Personaje es una secuencia de `PuntoRuta` que actúan como posiciones objetivo.

<div className="fd-steps">
  <div className="fd-step">
    ### Crear los puntos [#crear-los-puntos-step]

    Añade objetos que representen cada posición y configúralos como `PuntoRuta`.
  </div>

  <div className="fd-step">
    ### Ordenar la ruta [#ordenar-la-ruta-step]

    Mantén los puntos en el orden en el que debe recorrerlos el personaje. El controlador utiliza un índice sobre `camino`.
  </div>

  <div className="fd-step">
    ### Asignar la lista [#asignar-la-lista-step]

    Asigna los puntos a la lista `camino` de `Personaje`.
  </div>

  <div className="fd-step">
    ### Ajustar la distancia [#ajustar-la-distancia-step]

    `distanciaMinima` determina cuándo el personaje considera alcanzado el punto y pasa al siguiente.
  </div>

  <div className="fd-step">
    ### Verificar el ciclo [#verificar-el-ciclo-step]

    La implementación actual vuelve al primer punto cuando llega al último, por lo que una ruta se comporta como un recorrido cíclico.
  </div>
</div>
