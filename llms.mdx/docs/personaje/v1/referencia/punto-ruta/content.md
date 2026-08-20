# PuntoRuta (/docs/personaje/v1/referencia/punto-ruta)



`PuntoRuta` representa una posición de una ruta. `Personaje.camino` mantiene una lista ordenada de estos puntos.

El controlador utiliza el índice actual de la ruta para escoger el objetivo y avanza al siguiente punto cuando alcanza `distanciaMinima`.

Al completar la lista, la implementación actual vuelve al primer punto, por lo que el recorrido es cíclico.
