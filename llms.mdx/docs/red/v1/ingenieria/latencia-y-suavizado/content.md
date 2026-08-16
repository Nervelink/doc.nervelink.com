# Latencia y suavizado (/docs/red/v1/ingenieria/latencia-y-suavizado)



La red nunca entrega información exactamente cuando se produce. Una arquitectura robusta asume retraso, jitter y pérdida.

## Cuatro estrategias [#cuatro-estrategias]

### Estado remoto [#estado-remoto]

El cliente recibe una nueva muestra y actualiza su representación.

### Interpolación [#interpolación]

Cuando una entidad tiene movimiento continuo, el cliente interpola entre estados conocidos para evitar saltos.

### Predicción [#predicción]

El cliente puede adelantar localmente una acción cuya respuesta visual no necesita esperar al servidor.

### Reconciliación [#reconciliación]

Cuando llega el estado autoritativo, la representación local se corrige hacia ese estado.

## Qué usar en un Tower Defense [#qué-usar-en-un-tower-defense]

La mayoría de torres y enemigos no necesitan predicción compleja. Es preferible:

```text
Servidor → estado lógico
Cliente  → interpolación + animación
```

La predicción tiene más sentido para acciones directamente controladas por el jugador cuando esperar confirmación produce una sensación claramente mala.

<Callout title="No predigas reglas" type="warn">
  La predicción puede anticipar la presentación de una acción. No convierte al cliente en autoridad sobre daño, recursos, construcción o victoria.
</Callout>

## TransformSuave [#transformsuave]

Un sistema de suavizado debe considerarse una capa de presentación. No debería convertirse en una segunda simulación autoritativa.
