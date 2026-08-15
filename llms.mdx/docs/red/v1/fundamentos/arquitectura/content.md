# Arquitectura (/docs/red/v1/fundamentos/arquitectura)



# Arquitectura de Eco [#arquitectura-de-eco]

Eco es la capa de red utilizada por Pandora. Su implementación procede de TNet 3, pero expone una nomenclatura y una organización propias de Nervelink.

La documentación de Eco describe el comportamiento del código actual de `Nervelink/eco`. Las equivalencias con TNet se utilizan como referencia, no como contrato de la API.

<Callout title="Idea principal" type="info">
  Eco no debe entenderse como una colección de RPC aislados. Su arquitectura gira alrededor de conexiones, canales, jugadores, objetos de red, componentes, mensajes y sincronización de estado.
</Callout>

## Vista general [#vista-general]

```text
                              Eco
                               │
              ┌────────────────┼────────────────┐
              │                │                │
           Cliente          Servidor        Común
              │                │                │
       ┌──────┼──────┐         │        ┌───────┼────────┐
       │      │      │         │        │       │        │
   Conexión  Canal  Objetos   Canales  Paquetes  Buffer  Serialización
              │      │
              │      ├── Objeto
              │      │     └── Componente
              │      │
              └──────┴── Jugadores
```

## Capas principales [#capas-principales]

### Cliente [#cliente]

La capa cliente contiene la lógica que conecta una instancia del juego con un servidor, procesa el tráfico recibido y mantiene la representación local de los canales y objetos de red.

### Servidor [#servidor]

El servidor mantiene el estado compartido, administra canales y jugadores y procesa las operaciones que deben existir fuera de una instancia concreta del juego.

### Común [#común]

La capa común contiene los tipos que necesitan compartir cliente y servidor: paquetes, buffers, identificadores, serialización y estructuras utilizadas para representar el protocolo.

## Objetos de red [#objetos-de-red]

Un objeto de red representa una entidad que Eco puede identificar y sincronizar dentro de un canal.

```text
Objeto
├── Identidad
├── Propietario
├── Canal(es)
├── Estado
└── Componentes
```

## Canales [#canales]

El `Canal` delimita el ámbito en el que se comparte estado entre jugadores. Una conexión puede participar en uno o varios canales.

## Flujo conceptual de un mensaje [#flujo-conceptual-de-un-mensaje]

```text
Juego
  ↓
Objeto / Componente
  ↓
Eco
  ↓
Paquete
  ↓
Transporte
  ↓
Servidor
  ↓
Canal / destinatarios
  ↓
Paquete
  ↓
Cliente remoto
```

## Relación con TNet [#relación-con-tnet]

| TNet                    | Eco          |
| ----------------------- | ------------ |
| `TNManager`             | `Eco`        |
| `TNObject`              | `Objeto`     |
| `TNBehaviour`           | `Componente` |
| `TNChannel` / `Channel` | `Canal`      |
| `TNPacket` / `Packet`   | `Paquete`    |
| `TNBuffer`              | `Buffer`     |
| `Player`                | `Jugador`    |

## Referencias [#referencias]

<Card title="Repositorio Eco" href="https://github.com/Nervelink/eco">
  Código fuente de la implementación de Eco.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio original del que procede la base de Eco.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Documentación generada sobre el repositorio actual de TNet.
</Card>
