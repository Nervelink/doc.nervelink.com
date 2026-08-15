# Arquitectura (/docs/red/v1/arquitectura)



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

Dentro de `Core/Red/Cliente` se encuentran, entre otros, componentes como `ActualizadorRed`, `AutoCrear`, `AutoSincronizar`, `AutoUnirse` y `ClienteJuego`.

### Servidor [#servidor]

El servidor mantiene el estado compartido, administra canales y jugadores y procesa las operaciones que deben existir fuera de una instancia concreta del juego.

### Común [#común]

La capa común contiene los tipos que necesitan compartir cliente y servidor: paquetes, buffers, identificadores, serialización y estructuras utilizadas para representar el protocolo.

## Objetos de red [#objetos-de-red]

Un objeto de red representa una entidad que Eco puede identificar y sincronizar dentro de un canal.

Conceptualmente:

```text
Objeto
├── Identidad
├── Propietario
├── Canal(es)
├── Estado
└── Componentes
```

Los `Componente` proporcionan el comportamiento de red asociado al `Objeto`. Esta separación permite que un objeto represente la identidad de red mientras sus componentes implementan funciones concretas.

## Canales [#canales]

El `Canal` delimita el ámbito en el que se comparte estado entre jugadores. No debe confundirse con una conexión de transporte: una conexión puede participar en uno o varios canales.

La gestión detallada de canales, incluyendo su ciclo de vida y la participación simultánea en varios canales, se documentará en la sección dedicada a canales.

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
  ↓
Objeto / Componente
```

El transporte mueve los datos, pero el significado del mensaje pertenece a las capas superiores de Eco.

## Relación con TNet [#relación-con-tnet]

Eco conserva la arquitectura conceptual de TNet 3. Algunas equivalencias importantes son:

| TNet                    | Eco          |
| ----------------------- | ------------ |
| `TNManager`             | `Eco`        |
| `TNObject`              | `Objeto`     |
| `TNBehaviour`           | `Componente` |
| `TNChannel` / `Channel` | `Canal`      |
| `TNPacket` / `Packet`   | `Paquete`    |
| `TNBuffer`              | `Buffer`     |
| `Player`                | `Jugador`    |

Estas correspondencias son una ayuda para migrar conocimientos de TNet, pero no implican que las APIs deban seguir siendo idénticas.

## Fuente del comportamiento [#fuente-del-comportamiento]

El código de referencia de Eco se encuentra en el repositorio `Nervelink/eco`, principalmente bajo:

```text
src/Assets/Pandora/Logica/Nucleo/Core/Red/
```

La implementación de cliente ya contiene, entre otros elementos, actualización de red, creación automática, sincronización automática, unión automática y el cliente de juego.

## Referencias [#referencias]

<Card title="Repositorio Eco" href="https://github.com/Nervelink/eco">
  Código fuente de la implementación de Eco.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio original del que procede la base de Eco.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Documentación generada sobre el repositorio actual de TNet, útil para contrastar arquitectura y evolución upstream.
</Card>
