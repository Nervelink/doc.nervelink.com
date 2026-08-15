# Lobby y descubrimiento (/docs/red/v1/avanzado/lobby-y-discovery)



# Lobby y descubrimiento [#lobby-y-descubrimiento]

El lobby y el servidor de juego resuelven problemas diferentes. El lobby ayuda a encontrar o anunciar servidores; el servidor de juego mantiene la partida y su estado.

```text
Cliente
  │
  ├── Lobby / Discovery ──► lista de servidores
  │
  └── Servidor de juego ──► conexión de gameplay
```

<Callout title="El lobby no es la partida" type="info">
  Un servidor de lobby puede actuar como directorio o punto de descubrimiento. La autoridad sobre jugadores, canales y objetos continúa perteneciendo al servidor de juego.
</Callout>

## Flujo recomendado [#flujo-recomendado]

<Steps>
  <Step>
    ### Descubrir servidores [#descubrir-servidores]

    Consulta el servicio de lobby o el mecanismo de descubrimiento disponible.
  </Step>

  <Step>
    ### Elegir un servidor [#elegir-un-servidor]

    Selecciona el endpoint de gameplay según latencia, región, capacidad o estado anunciado.
  </Step>

  <Step>
    ### Conectar directamente [#conectar-directamente]

    Usa `Eco.Conectar(...)` contra el servidor de juego elegido.
  </Step>

  <Step>
    ### Entrar en un canal [#entrar-en-un-canal]

    Una vez conectada la sesión, la lógica de partida pasa a los canales de Eco.
  </Step>
</Steps>

## Heartbeats y estado anunciado [#heartbeats-y-estado-anunciado]

El material upstream describe mecanismos de lobby que utilizan mensajes de control, ping y actualizaciones del estado de los servidores. El objetivo es evitar que el directorio necesite replicar todo el estado de la partida.

```text
Servidor de juego
   │
   ├── nombre
   ├── jugadores
   ├── capacidad
   ├── estado
   └── endpoint
          ↓
       Lobby
          ↓
       Clientes
```

## Cuándo necesitas un lobby [#cuándo-necesitas-un-lobby]

No todos los proyectos lo requieren.

| Arquitectura               | Lobby                                 |
| -------------------------- | ------------------------------------- |
| Cliente → IP fija          | No                                    |
| Servidor dedicado listado  | Normalmente sí                        |
| Matchmaking propio         | Depende                               |
| LAN                        | Puede sustituirse por discovery local |
| Steam / plataforma externa | Puede recaer en la plataforma         |

## Discovery no equivale a NAT traversal [#discovery-no-equivale-a-nat-traversal]

Encontrar un servidor y conseguir que un cliente pueda alcanzarlo son problemas diferentes. UPnP, port forwarding, Steam Networking y otras técnicas pertenecen a la segunda parte.

<Card title="UPnP y NAT" href="/docs/red/v1/avanzado/upnp">
  Conectividad y exposición del servidor.
</Card>

<Card title="Steam y conexiones" href="/docs/red/v1/avanzado/steam-y-conexiones">
  Transportes de plataforma y conexiones personalizadas.
</Card>

## Relación con TNet [#relación-con-tnet]

DeepWiki mantiene páginas separadas para lobby cliente, lobby servidor y discovery. En Eco la documentación debe conservar esa separación conceptual, pero las APIs deben seguir las implementaciones reales disponibles en el repositorio de Eco.

<Card title="DeepWiki · Lobby y discovery" href="https://deepwiki.com/tasharen/tnet">
  Referencia upstream para arquitectura de lobby y descubrimiento.
</Card>
