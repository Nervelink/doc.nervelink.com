# Unirse a amigos (/docs/red/v1/steam/unirse-a-amigos)



La integración utiliza Rich Presence para publicar un valor `connect` que contiene la información necesaria para iniciar la conexión.

## Flujo [#flujo]

```text
Servidor Eco
   ↓
Steam.PermitirUnirseAmigos(true)
   ↓
Rich Presence: connect
   ↓
Amigo abre "Unirse al juego"
   ↓
Steam inicia la aplicación
   ↓
Argumento +connect
   ↓
Steam.ArgumentoAutoConectar
   ↓
Steam.Conectar(...)
   ↓
Eco.Conectar(...)
```

El código actual analiza `+connect` en los argumentos de línea de comandos para detectar este arranque automático. fileciteturn190file0

## Permitir unirse [#permitir-unirse]

`Steam.PermitirUnirseAmigos(true)` publica la información de conexión cuando existe una sesión que pueda recibir jugadores. Si se desactiva, limpia el campo `connect`.

El comportamiento depende de si Eco está actuando como servidor, utiliza una conexión P2P personalizada o dispone de un punto TCP. fileciteturn190file0

## Cuándo habilitarlo [#cuándo-habilitarlo]

No lo actives automáticamente durante todo el ciclo de vida si tu juego tiene matchmaking, lobby privado o estados donde no se debe admitir a nuevos jugadores.

```text
Menú        → desactivado
Lobby       → según diseño
Partida     → normalmente activado si se permiten entradas
Fin partida → desactivado
```

<Callout title="Control de acceso" type="info">
  Rich Presence anuncia una ruta de conexión; no sustituye la autorización de tu servidor. La partida debe validar al jugador cuando intenta entrar.
</Callout>
