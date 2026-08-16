# Modelo mental (/docs/red/v1/ingenieria/modelo-mental)



Antes de utilizar la API conviene separar cinco conceptos: **intención, estado, autoridad, transporte y presentación**.

| Concepto     | Pregunta                           |
| ------------ | ---------------------------------- |
| Intención    | ¿Qué quiere hacer el jugador?      |
| Estado       | ¿Qué es cierto ahora mismo?        |
| Autoridad    | ¿Quién puede decidirlo?            |
| Transporte   | ¿Cómo llega a otros participantes? |
| Presentación | ¿Cómo lo mostramos localmente?     |

### Intención [#intención]

Construir, mejorar, vender, activar una habilidad o cambiar una orden son acciones solicitadas por un participante. El cliente expresa la intención; el servidor decide si puede ejecutarse.

### Estado [#estado]

Vida, recursos, nivel, propietario, estado de una unidad u oleada son datos que pueden necesitar sincronización. No deben confundirse con el comando que los cambió.

### Evento [#evento]

Un evento informa de que algo ocurrió. Puede ser útil para efectos que no necesitan convertirse en estado persistente.

### Presentación [#presentación]

Animaciones, VFX, cámara y UI deben reconstruirse a partir del estado siempre que sea posible. Convertirlos en tráfico de red aumenta coste y acoplamiento.

<Callout title="Regla de diseño" type="info">
  Si puedes eliminar un dato de red y reconstruirlo de forma determinista o a partir del estado recibido, elimínalo de la red.
</Callout>

## Modelo práctico [#modelo-práctico]

```text
Jugador
  │ intención
  ▼
Servidor
  │ valida
  ▼
Estado
  │
  ▼
Eco
  │
  ├── Cliente A → presentación
  ├── Cliente B → presentación
  └── Cliente C → presentación
```
