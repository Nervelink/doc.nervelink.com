# Instalación (/docs/red/v1/steam/instalacion)



La integración se encuentra dentro del núcleo de red de Eco y depende de Steamworks cuando la compilación de Steam está habilitada. El código actual está protegido mediante compilación condicional para evitar exigir Steam en plataformas donde no corresponde. fileciteturn190file0

<Steps>
  <Step>
    Integra Steamworks.NET o la capa Steamworks compatible con tu proyecto.
  </Step>

  <Step>
    Configura el App ID del juego en la integración de Steam.
  </Step>

  <Step>
    Asegúrate de que Steamworks puede inicializarse correctamente en la build.
  </Step>

  <Step>
    Deja que Eco inicialice el sistema Steam durante su ciclo de sistemas.
  </Step>

  <Step>
    Comprueba identidad, callbacks y conexión antes de probar el flujo de amigos.
  </Step>
</Steps>

## Orden recomendado [#orden-recomendado]

Primero valida:

```text
Steam inicializa
      ↓
Steam ID disponible
      ↓
Callbacks funcionando
      ↓
Conexión P2P
      ↓
Eco conectado
      ↓
Lobby
      ↓
Partida
```

No intentes depurar lobby, Rich Presence y gameplay simultáneamente durante la primera integración.
