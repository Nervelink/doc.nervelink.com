# Locomoción y sensores (/docs/personaje/v1/fundamentos/locomocion-y-sensores)



`Locomocion` es la capa que recibe la velocidad calculada por el controlador y se encarga de afinar y aplicar el movimiento sobre el GameObject.

## Flujo [#flujo]

```text
Controlador
  ↓ velocidad
Locomocion
  ↓
Sensor
  ↓
Estado físico
  ↓
Movimiento aplicado
```

## Sensor [#sensor]

El sensor comprueba información del entorno necesaria para saber si el personaje está sobre una superficie, cómo responde a pendientes y cuándo deben actualizarse sus comprobaciones.

Los controladores utilizan métodos como `ComprobarSuelo`, `EnSuelo`, `RangoExtendidoSensor` y `ActualizarSensor` para coordinar estas comprobaciones con el ciclo físico.

## Jugador y pendientes [#jugador-y-pendientes]

`Jugador` añade `limitePendiente` y `gravedadPendiente`. El estado puede pasar a `Deslizando` cuando la superficie excede el límite configurado.

## DetectorTecho [#detectortecho]

`DetectorTecho` es una capa específica del jugador que registra colisiones que representan un techo y permite reaccionar ante golpes verticales durante el salto.

## Por qué no mover todo a un Rigidbody [#por-qué-no-mover-todo-a-un-rigidbody]

La arquitectura mantiene el cálculo de movimiento en los controladores y centraliza la aplicación física en `Locomocion`, lo que facilita reutilizar la misma infraestructura entre distintos tipos de personaje.
