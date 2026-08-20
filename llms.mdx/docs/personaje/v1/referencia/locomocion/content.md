# Locomocion (/docs/personaje/v1/referencia/locomocion)



`Locomocion` recibe velocidades calculadas por `Personaje` o `Jugador` y coordina la aplicación del movimiento con los sensores.

## Responsabilidad [#responsabilidad]

No decide si el jugador quiere caminar o saltar. Su papel es ejecutar y ajustar el movimiento solicitado por el controlador.

## Flujo habitual [#flujo-habitual]

```text
Controlador
  ↓
EstablecerVelocidad
  ↓
Sensor
  ↓
Movimiento físico
```

## Métodos relevantes [#métodos-relevantes]

`Inicializar()` prepara el estado interno.

`IniciarSensor(GameObject)` conecta la locomoción con el objeto y su detección del entorno.

`ComprobarSuelo()` actualiza el estado de superficie.

`EnSuelo()` devuelve si el personaje está apoyado.

`RangoExtendidoSensor(bool)` permite ajustar la comprobación del sensor al estado del personaje.

`EstablecerVelocidad(Vector3)` aplica la velocidad calculada por el controlador.

`ActualizarSensor()` actualiza el sensor en el ciclo tardío.
