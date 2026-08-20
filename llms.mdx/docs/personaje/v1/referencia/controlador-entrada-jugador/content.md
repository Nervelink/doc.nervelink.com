# ControladorEntradaJugador (/docs/personaje/v1/referencia/controlador-entrada-jugador)



`ControladorEntradaJugador` desacopla el dispositivo de entrada de la lógica de movimiento.

`Jugador` lo crea si no existe y expone la instancia mediante `ObtenerEntrada()`.

## Responsabilidad [#responsabilidad]

Su función es traducir entrada a intención. No aplica gravedad, no mueve el transform y no decide cómo resolver una colisión.
