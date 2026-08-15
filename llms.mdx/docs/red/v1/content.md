# Red · Eco v1 (/docs/red/v1)



# Red · Eco v1 [#red--eco-v1]

Eco es la capa de networking de Pandora. Su implementación parte de TNet 3 y conserva su modelo de comunicación, canales, objetos de red, sincronización y persistencia, pero expone una API adaptada a la nomenclatura y arquitectura de Nervelink.

La documentación de esta versión se centra en el comportamiento real de `eco`, no en los ejemplos históricos de TNet.

## Mapa rápido [#mapa-rápido]

```text
Eco
├── Cliente
├── Común
├── Servidor
├── Serialización y buffers
├── Sincronización
└── Objetos y canales
```

## Conceptos principales [#conceptos-principales]

| TNet                    | Eco               | Propósito                                    |
| ----------------------- | ----------------- | -------------------------------------------- |
| `TNManager`             | `Eco`             | Punto de entrada y gestión de la red         |
| `TNObject`              | `Objeto`          | Identidad y estado de un objeto de red       |
| `TNBehaviour`           | `Componente`      | Comportamiento de red asociado a un objeto   |
| `Channel` / `TNChannel` | `Canal`           | Agrupa jugadores y estado compartido         |
| `Packet` / `TNPacket`   | `Paquete`         | Representa mensajes del protocolo            |
| `TNBuffer`              | `Buffer`          | Lectura y escritura binaria                  |
| `TNList`                | `Lista`           | Colección utilizada por la infraestructura   |
| `TNDatagram`            | `Datagrama`       | Unidad asociada al transporte de datagramas  |
| `TNFieldOrProperty`     | `CampoOPropiedad` | Abstracción para acceso reflejado a miembros |

## Estructura del código [#estructura-del-código]

La implementación se encuentra en el proyecto Unity de `Nervelink/eco`, principalmente bajo:

```text
src/Assets/Pandora/Logica/Nucleo/Core/Red/
```

La estructura separa principalmente código de cliente y código común, además de las áreas auxiliares de runtime, ejemplos y compresión.

## Principios de esta documentación [#principios-de-esta-documentación]

Esta documentación distingue entre tres cosas:

* comportamiento heredado de TNet;
* cambios realizados por Eco;
* decisiones propias de la arquitectura de Pandora.

Cuando una página describe una API concreta, la fuente de referencia es el código de `eco` y no un tutorial antiguo de TNet.
