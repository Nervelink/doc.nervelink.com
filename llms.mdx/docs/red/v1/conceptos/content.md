# Conceptos fundamentales (/docs/red/v1/conceptos)



# Conceptos fundamentales [#conceptos-fundamentales]

Antes de trabajar con Eco conviene entender unas pocas piezas que aparecen constantemente en su API. Eco hereda el modelo conceptual de TNet, pero la documentación utiliza siempre los nombres de Eco.

<Callout title="Regla general" type="info">
  Una conexión representa la comunicación con el servidor; un jugador representa a quién pertenece esa conexión; un canal define un ámbito de estado compartido; un objeto representa una entidad de red; y sus componentes implementan el comportamiento.
</Callout>

## Conexión [#conexión]

La conexión representa el vínculo de transporte entre una instancia del juego y el servidor. Es responsable de llevar y recibir los datos, pero no define por sí sola qué objetos o estado debe conocer el cliente.

```text
Cliente
  │
  └── Conexión ─────── Servidor
```

Por eso una conexión no debe confundirse con un canal. La misma conexión puede participar en diferentes canales según el estado de la sesión.

## Jugador [#jugador]

Un `Jugador` representa a un participante de la sesión dentro de la infraestructura de red. El jugador está asociado a una conexión y sirve para identificar al participante cuando Eco necesita expresar propiedad, destinatarios o presencia dentro de un canal.

```text
Conexión
   │
   └── Jugador
```

El jugador no es necesariamente la entidad jugable que existe en el mundo del juego. Esa entidad puede estar representada por un `Objeto` independiente.

## Canal [#canal]

Un `Canal` define un ámbito de estado compartido. Los jugadores pueden entrar y salir de canales y el servidor utiliza el canal como contexto para gestionar objetos, mensajes y persistencia.

```text
Servidor
  │
  ├── Canal A
  │    ├── Jugador 1
  │    └── Jugador 2
  │
  └── Canal B
       ├── Jugador 2
       └── Jugador 3
```

La participación en varios canales es una capacidad importante de Eco. No debe modelarse como si el cliente sólo pudiera tener un canal activo.

## Objeto [#objeto]

Un `Objeto` es una identidad de red que Eco puede registrar, localizar, sincronizar y destruir.

Un objeto puede entenderse como la unidad sobre la que se construye el estado de una entidad dentro de la red:

```text
Objeto
├── ID de red
├── Propietario
├── Estado
├── Canal(es)
└── Componentes
```

El objeto no representa por sí mismo toda la lógica de gameplay. Su función es proporcionar la identidad y el contexto de red sobre el que trabajan sus componentes.

## Componente [#componente]

Un `Componente` contiene comportamiento de red asociado a un `Objeto`. Es el equivalente conceptual del componente de comportamiento de TNet y permite separar la identidad de red de la funcionalidad concreta.

```text
Objeto
├── Componente A
├── Componente B
└── Componente C
```

Esta separación es especialmente útil cuando una misma entidad necesita varias responsabilidades de red: sincronización, llamadas remotas, lógica de propiedad, etc.

## Propietario [#propietario]

Eco mantiene información de propiedad sobre los objetos de red. El propietario identifica al participante que tiene autoridad sobre determinadas operaciones del objeto.

```text
Objeto
  │
  └── Propietario → Jugador
```

La propiedad no equivale necesariamente a que el objeto sólo exista para ese jugador. El objeto puede ser visible para otros participantes mientras uno de ellos mantiene su autoridad.

La semántica exacta de `isMine`, propietarios y transferencia de propiedad se documentará en la sección específica de objetos.

## Paquete [#paquete]

Un `Paquete` representa una unidad de datos del protocolo de Eco. Los paquetes transportan comandos, eventos y datos entre clientes y servidor.

Conceptualmente:

```text
Objeto / Componente
        ↓
     Paquete
        ↓
   Transporte
```

El paquete pertenece a la capa de protocolo. No es equivalente a un evento de gameplay: un evento de juego puede terminar representándose mediante uno o varios tipos de mensaje de red.

## Buffer [#buffer]

Un `Buffer` proporciona las operaciones de lectura y escritura utilizadas para serializar los datos que Eco intercambia.

```text
Dato
 ↓
Buffer
 ↓
Paquete
 ↓
Transporte
```

El buffer es deliberadamente una capa inferior a los objetos y componentes. Normalmente una característica de gameplay no debería manipularlo directamente salvo que esté implementando serialización o un protocolo específico.

## Estado [#estado]

Eco diferencia conceptualmente entre enviar una acción y mantener un estado de red. Una llamada remota puede comunicar que algo ocurrió, mientras que la sincronización de estado permite que un cliente conozca el valor actual de una propiedad o conjunto de datos.

Esta distinción será importante al documentar `RFC`, sincronización automática, sincronización personalizada y los mecanismos basados en datos.

## Relación entre conceptos [#relación-entre-conceptos]

```text
                         Servidor
                            │
              ┌─────────────┴─────────────┐
              │                           │
          Conexiones                  Canales
              │                           │
           Jugadores              ┌──────┴──────┐
              │                    │             │
              └──────────────┬─────┘             │
                             │                   │
                          Objetos ◄──────────────┘
                             │
                        Componentes
                             │
                    RFC / Sync / Datos
                             │
                          Paquetes
                             │
                         Transporte
```

La idea clave es que estas piezas no son alternativas entre sí. Forman capas distintas del modelo de red.

## Correspondencia con TNet [#correspondencia-con-tnet]

| Concepto Eco | TNet histórico          | Función                                 |
| ------------ | ----------------------- | --------------------------------------- |
| `Eco`        | `TNManager`             | Entrada y gestión principal del cliente |
| `Conexión`   | Conexiones de TNet      | Transporte entre cliente y servidor     |
| `Jugador`    | `Player`                | Participante de la sesión               |
| `Canal`      | `Channel` / `TNChannel` | Ámbito de estado compartido             |
| `Objeto`     | `TNObject`              | Identidad de red                        |
| `Componente` | `TNBehaviour`           | Comportamiento asociado al objeto       |
| `Paquete`    | `Packet` / `TNPacket`   | Mensaje del protocolo                   |
| `Buffer`     | `TNBuffer`              | Serialización binaria                   |

La tabla sirve para orientarse al consultar documentación o código de TNet. Para implementar Eco debe utilizarse la API y el comportamiento del repositorio `Nervelink/eco`.

## Referencias upstream [#referencias-upstream]

<Card title="TNet en GitHub" href="https://github.com/tasharen/tnet">
  Repositorio original y fuente principal de referencia de la implementación heredada.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Vista generada de la arquitectura del repositorio actual de TNet. Útil para localizar subsistemas y contrastar su evolución.
</Card>
