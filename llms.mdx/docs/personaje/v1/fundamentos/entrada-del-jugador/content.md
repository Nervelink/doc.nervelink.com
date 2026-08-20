# Entrada del jugador (/docs/personaje/v1/fundamentos/entrada-del-jugador)



`Jugador` no acopla la lógica de movimiento a una acción concreta del teclado o mando. Utiliza `ControladorEntradaJugador` como capa intermedia.

```text
Input del usuario
      ↓
ControladorEntradaJugador
      ↓
Jugador
      ↓
Movimiento / salto
```

## Ventajas [#ventajas]

* permite cambiar el backend de entrada sin reescribir el controlador;
* separa intención de resolución física;
* facilita pruebas del movimiento sin depender directamente de UI o dispositivos.

## Movimiento relativo a cámara [#movimiento-relativo-a-cámara]

`Jugador.trCamara` puede definir una referencia para calcular el movimiento respecto a la orientación de la cámara. Si no se asigna, el controlador utiliza sus propios ejes.

## Salto [#salto]

`Jugador` distingue entre salto pulsado, mantenido y soltado y utiliza `velSalto` y `duracionSalto` para controlar la respuesta. Esto permite implementar saltos variables sin mezclar la lectura de entrada con la aplicación física.
