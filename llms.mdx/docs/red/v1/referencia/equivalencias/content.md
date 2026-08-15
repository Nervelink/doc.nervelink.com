# Equivalencias Eco ↔ TNet (/docs/red/v1/referencia/equivalencias)



# Equivalencias Eco ↔ TNet [#equivalencias-eco--tnet]

Eco mantiene una relación directa con muchas de las abstracciones de TNet. Esta tabla permite localizar rápidamente el concepto equivalente cuando se consulta código, documentación o ejemplos upstream.

| Eco               | TNet                    | Función                             |
| ----------------- | ----------------------- | ----------------------------------- |
| `Eco`             | `TNManager`             | Gestión principal del networking    |
| `Objeto`          | `TNObject`              | Identidad de red                    |
| `Componente`      | `TNBehaviour`           | Comportamiento asociado a un objeto |
| `Canal`           | `TNChannel` / `Channel` | Ámbito de estado compartido         |
| `Jugador`         | `Player`                | Participante de la sesión           |
| `Paquete`         | `Packet` / `TNPacket`   | Tipo de mensaje del protocolo       |
| `Buffer`          | `TNBuffer` / `Buffer`   | Lectura y escritura binaria         |
| `Nodo`            | `DataNode`              | Datos jerárquicos                   |
| `Lista<T>`        | `TList<T>`              | Colección interna                   |
| `Serializacion`   | `TNet.Serialization`    | Serialización y conversiones        |
| `ClienteJuego`    | `GameClient`            | Implementación del cliente          |
| `ServidorJuego`   | `GameServer`            | Implementación del servidor         |
| `ProtocoloTcp`    | `TcpProtocol`           | Transporte TCP                      |
| `ProtocoloUdp`    | `UdpProtocol`           | Transporte UDP                      |
| `AutoSincronizar` | `TNAutoSync`            | Sincronización automática           |

## Cómo utilizar la tabla [#cómo-utilizar-la-tabla]

La equivalencia sirve para traducir conceptos, no para asumir compatibilidad literal de APIs.

Por ejemplo, si un ejemplo de TNet habla de `TNObject`, el punto de partida equivalente en Eco es `Objeto`. A partir de ahí debe consultarse la API de Eco, ya que otros tipos y nombres pueden haber cambiado.

```text
TNet
TNObject
  ↓
Eco
Objeto
```

## Cambios de nomenclatura [#cambios-de-nomenclatura]

La refactorización de Eco busca que los nombres estén alineados con la nomenclatura de Pandora. Esto permite que el código de red se integre con el resto del proyecto sin mantener permanentemente la terminología histórica de TNet.

## Cambios de comportamiento [#cambios-de-comportamiento]

Una equivalencia de nombres no implica que las dos implementaciones tengan exactamente el mismo comportamiento. Eco puede haber incorporado refactorizaciones, cambios de arquitectura o funcionalidades propias.

La referencia definitiva siempre debe ser el código actual de `Nervelink/eco`.
