# Conceptos (/docs/red/v1/fundamentos/conceptos)



# Conceptos [#conceptos]

Eco organiza la red alrededor de unas pocas piezas fundamentales. Entender su relación evita confundir conexión, canal, jugador, objeto y componente.

<Callout title="Regla general" type="info">
  Una conexión representa la comunicación con el servidor; un jugador representa a un participante; un canal define un ámbito de estado compartido; un objeto representa una entidad de red; y sus componentes implementan el comportamiento.
</Callout>

## Conexión [#conexión]

La conexión representa el vínculo de transporte entre una instancia del juego y el servidor. No define por sí sola qué objetos o estado debe conocer el cliente.

## Jugador [#jugador]

Un `Jugador` representa a un participante de la sesión dentro de la infraestructura de red. Sirve para identificar presencia, propiedad y destinatarios.

## Canal [#canal]

Un `Canal` define un ámbito de estado compartido. Un mismo jugador puede participar simultáneamente en varios canales.

```text
Servidor
  ├── Canal A → Jugador 1, Jugador 2
  └── Canal B → Jugador 2, Jugador 3
```

## Objeto [#objeto]

Un `Objeto` es una identidad de red que Eco puede registrar, localizar, sincronizar y destruir.

```text
Objeto
├── ID de red
├── Propietario
├── Estado
├── Canal(es)
└── Componentes
```

## Componente [#componente]

Un `Componente` contiene comportamiento de red asociado a un `Objeto`. Esta separación permite mantener la identidad de red independiente de la funcionalidad concreta.

## Propietario [#propietario]

El propietario identifica al jugador que tiene autoridad sobre determinadas operaciones del objeto. La propiedad no implica que el objeto sea invisible para los demás participantes.

## Paquete [#paquete]

Un `Paquete` representa una unidad del protocolo de Eco. Transporta comandos, eventos y datos entre clientes y servidor.

## Buffer [#buffer]

Un `Buffer` proporciona las operaciones de lectura y escritura utilizadas para serializar los datos que Eco intercambia.

## Estado [#estado]

Eco distingue entre comunicar una acción y mantener un estado. Una RFC puede comunicar que algo ocurrió; un mecanismo de sincronización mantiene valores que deben converger entre participantes.

## Relación entre conceptos [#relación-entre-conceptos]

```text
Servidor
 │
 ├── Conexiones
 │     └── Jugadores
 │
 └── Canales
       └── Objetos
             └── Componentes
                   ├── RFC
                   ├── Sync
                   └── Datos
                         ↓
                      Paquetes
                         ↓
                      Transporte
```

## Correspondencia con TNet [#correspondencia-con-tnet]

| Eco          | TNet                    | Función                           |
| ------------ | ----------------------- | --------------------------------- |
| `Eco`        | `TNManager`             | Gestión principal del cliente     |
| `Jugador`    | `Player`                | Participante de la sesión         |
| `Canal`      | `Channel` / `TNChannel` | Ámbito de estado compartido       |
| `Objeto`     | `TNObject`              | Identidad de red                  |
| `Componente` | `TNBehaviour`           | Comportamiento asociado al objeto |
| `Paquete`    | `Packet` / `TNPacket`   | Mensaje del protocolo             |
| `Buffer`     | `TNBuffer`              | Serialización binaria             |

## Referencias [#referencias]

<Card title="TNet en GitHub" href="https://github.com/tasharen/tnet">
  Repositorio upstream y fuente de referencia de la implementación heredada.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Vista generada de la arquitectura actual de TNet.
</Card>
