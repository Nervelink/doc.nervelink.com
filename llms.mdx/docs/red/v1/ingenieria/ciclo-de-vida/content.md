# Ciclo de vida (/docs/red/v1/ingenieria/ciclo-de-vida)



Una sesión de red debe entenderse como un ciclo completo, no como una llamada aislada a conectar.

```text
Aplicación
  ↓
Eco inicializa
  ↓
Transporte disponible
  ↓
Conexión
  ↓
Handshake
  ↓
Jugador identificado
  ↓
Entrada en canal
  ↓
Creación / recepción de objetos
  ↓
Sincronización
  ↓
Gameplay
  ↓
Desconexión
  ↓
Limpieza / persistencia
```

## Arranque [#arranque]

El sistema de red debe inicializarse antes de que otros sistemas dependan de un estado conectado. Los sistemas de gameplay deberían poder representar explícitamente estados como `Desconectado`, `Conectando`, `Conectado` y `EnPartida`.

## Entrada en un canal [#entrada-en-un-canal]

El canal define el ámbito en el que el participante recibe objetos y datos. Entrar en un canal no significa necesariamente estar autorizado para todas las acciones del juego: esa decisión pertenece al dominio.

## Creación de objetos [#creación-de-objetos]

Un cliente que entra tarde debe poder recibir suficiente estado para reconstruir la escena de gameplay. Esto es una de las razones por las que la persistencia y el estado sincronizado deben diseñarse desde el principio.

## Desconexión [#desconexión]

La salida debe provocar una política explícita para cada recurso propiedad del participante: destruir, transferir, conservar temporalmente o convertirlo en propiedad del servidor.

<Callout title="Caso crítico" type="warn">
  Diseña la desconexión antes de terminar el sistema de ownership. Si no existe una política clara, aparecerán objetos huérfanos, estados inconsistentes o recursos perdidos.
</Callout>
