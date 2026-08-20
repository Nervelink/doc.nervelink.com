# Sensor (/docs/personaje/v1/referencia/sensor)



`Sensor` proporciona a la locomoción la información del entorno necesaria para resolver apoyo, superficies y actualización física.

## Responsabilidad [#responsabilidad]

El sensor no mueve al personaje. Detecta el entorno y publica el estado que necesita `Locomocion`.

## Integración [#integración]

```text
Sensor
  ↓ estado del entorno
Locomocion
  ↓
Personaje / Jugador
```

La actualización del sensor se coordina con el ciclo físico para evitar que el controlador trabaje con información de una posición anterior.
