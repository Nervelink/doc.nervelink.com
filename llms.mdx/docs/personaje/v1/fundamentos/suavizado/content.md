# Suavizado (/docs/personaje/v1/fundamentos/suavizado)



El módulo incluye `SuavizadoPosicion` y `SuavizadoRotacion` para evitar transiciones bruscas entre estados de transform.

## Cuándo utilizarlo [#cuándo-utilizarlo]

Es útil cuando una posición o rotación cambia por una fuente discreta: red, teletransporte, cambio de objetivo o actualización externa.

## Qué no hace [#qué-no-hace]

El suavizado no resuelve la autoridad, la predicción ni la sincronización. Sólo decide cómo llegar visualmente de un estado a otro.

```text
Estado recibido
     ↓
Suavizado
     ↓
Transform visual
```

En un juego en red puede utilizarse junto a Eco, pero debe permanecer separado del estado autoritativo que se sincroniza.
