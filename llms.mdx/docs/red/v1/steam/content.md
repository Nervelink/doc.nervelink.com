# Steam (/docs/red/v1/steam)



Eco incluye una integración de Steam que actúa como capa de transporte e identidad sin obligar al gameplay a conocer los detalles de Steam.

<Cards>
  <Card title="Instalación" href="/docs/red/v1/steam/instalacion">
    Preparar Steamworks y Eco.
  </Card>

  <Card title="Conexión P2P" href="/docs/red/v1/steam/conexion-p2p">
    Transportar una sesión Eco entre usuarios de Steam.
  </Card>

  <Card title="Unirse a amigos" href="/docs/red/v1/steam/unirse-a-amigos">
    Invitaciones, Rich Presence y conexión automática.
  </Card>

  <Card title="Lobby y partidas" href="/docs/red/v1/steam/lobby-y-partidas">
    Separar Steam, lobby Eco y gameplay.
  </Card>

  <Card title="Diagnóstico" href="/docs/red/v1/steam/diagnostico">
    Comprobar Steamworks, callbacks y conectividad.
  </Card>
</Cards>

## Arquitectura [#arquitectura]

```text
Steamworks
   │
   ├── identidad
   ├── amigos
   ├── Rich Presence
   └── P2P
          │
          ▼
     Adaptador Steam
          │
          ▼
       IConnection
          │
          ▼
          Eco
          │
          ▼
      Juego / Objetos
```

El objetivo es que el código de gameplay siga trabajando con Eco aunque el transporte concreto sea Steam.

## Qué aporta Steam [#qué-aporta-steam]

* identidad Steam del jugador;
* identificación de amigos;
* conexión P2P;
* Rich Presence;
* entrada desde la opción de unirse a una partida;
* callbacks de Steamworks;
* transporte de paquetes de Eco.

La integración actual expone además `IDUsuario`, `NombreUsuario`, `ArgumentoAutoConectar`, `Conectar`, `PermitirUnirseAmigos`, `ObtenerAmigosConPartida` y `AbrirOverlayInvitar`. fileciteturn190file0

## Qué no aporta Steam [#qué-no-aporta-steam]

Steam no decide la autoridad de tu partida, el ownership de tus objetos, la lógica de combate ni la estructura de tus canales. Es una capa de plataforma y transporte.

<Callout title="Importante" type="info">
  Una conexión Steam válida no significa que el jugador esté dentro de una partida Eco. Steam proporciona el camino y la identidad; Eco mantiene la sesión de juego.
</Callout>
