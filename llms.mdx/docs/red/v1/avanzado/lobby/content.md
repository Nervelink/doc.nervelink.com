# Lobby (/docs/red/v1/avanzado/lobby)



# Lobby [#lobby]

La carpeta `Sala` implementa la comunicación entre servidores de juego y un servicio de lobby. Su objetivo es anunciar servidores disponibles y permitir que los clientes consulten la lista sin conocer de antemano sus endpoints.

## Arquitectura [#arquitectura]

```text
ServidorJuego
     │
     ▼
EnlaceLobby
     │
     ▼
LobbyServidor
     ▲
     │
LobbyCliente
     │
     ▼
Juego
```

La implementación incluye variantes TCP y UDP para el enlace.

## Cuándo utilizarlo [#cuándo-utilizarlo]

Un lobby tiene sentido cuando el juego necesita:

* lista pública de partidas;
* descubrimiento de servidores;
* actualización automática de jugadores y endpoints;
* separación entre el servicio de descubrimiento y el servidor de gameplay.

No necesitas lobby para una conexión directa a IP.

## Datos publicados [#datos-publicados]

El servidor puede anunciar información como nombre, número de jugadores, ID de juego y endpoints de conexión. La comunicación entre lobby y game server no debe confundirse con el canal donde sucede la partida.

<Callout title="Lobby ≠ canal" type="info">
  El lobby descubre servidores. El canal organiza jugadores y estado dentro de un servidor de juego. Son dos niveles diferentes.
</Callout>

## Componentes [#componentes]

```text
Sala/
├── EnlaceLobby
├── EnlaceLobbyTcp
├── EnlaceLobbyUdp
├── LobbyCliente
├── LobbyClienteTcp
├── LobbyClienteUdp
└── LobbyServidor
```

<Card title="Sistema de lobby" href="https://github.com/Nervelink/eco/tree/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Sala">
  Código fuente actual del subsistema.
</Card>

<Card title="ServidorJuego" href="/docs/red/v1/referencia/api/servidor-juego">
  Integración del lobby desde el servidor.
</Card>
