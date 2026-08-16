# Lobby y partidas (/docs/red/v1/steam/lobby-y-partidas)



Steam puede facilitar descubrimiento y unión, pero el lobby de gameplay debe seguir teniendo un estado propio.

## Separación recomendada [#separación-recomendada]

```text
Steam
├── identidad
├── amigos
└── descubrimiento

Eco
├── lobby
│   ├── jugadores
│   ├── ready
│   └── configuración
│
└── partida
    ├── canal
    ├── objetos
    └── estado
```

## Flujo cooperativo [#flujo-cooperativo]

<Steps>
  <Step>
    El anfitrión inicia la sesión Eco.
  </Step>

  <Step>
    Steam publica la información de unión si está permitido.
  </Step>

  <Step>
    Los amigos se conectan mediante Steam P2P.
  </Step>

  <Step>
    Eco identifica y valida al nuevo jugador.
  </Step>

  <Step>
    El jugador entra al canal de lobby.
  </Step>

  <Step>
    El lobby sincroniza raza, preparación y configuración.
  </Step>

  <Step>
    El servidor inicia la partida y establece el ámbito de gameplay.
  </Step>
</Steps>

## Por qué no usar Steam como autoridad [#por-qué-no-usar-steam-como-autoridad]

Steam conoce la identidad y el transporte, pero no conoce las reglas de tu partida. Eco debe decidir si un jugador puede entrar, qué canal recibe y qué objetos se crean.

## Hasta cuatro jugadores [#hasta-cuatro-jugadores]

Para un juego cooperativo de cuatro jugadores, el límite de Steam y el límite de gameplay deben considerarse por separado. El servidor debe rechazar jugadores adicionales aunque el transporte permita técnicamente una conexión.
