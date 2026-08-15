# Paquetes y protocolo (/docs/red/v1/transporte/paquetes-protocolo)



# Paquetes y protocolo [#paquetes-y-protocolo]

`Paquete` no representa un objeto de gameplay: identifica una operación del protocolo. El cliente y el servidor utilizan estos valores para saber cómo interpretar el contenido que sigue.

## Capas [#capas]

```text
RFC / estado
     ↓
Paquete
     ↓
Buffer
     ↓
TCP / UDP / conexión personalizada
```

Cada capa responde a una pregunta diferente:

| Capa         | Pregunta                             |
| ------------ | ------------------------------------ |
| RFC / estado | ¿Qué quiere hacer el juego?          |
| `Paquete`    | ¿Qué operación de protocolo es?      |
| `Buffer`     | ¿Cómo están representados los bytes? |
| Transporte   | ¿Cómo llegan los bytes?              |

## Tipos importantes [#tipos-importantes]

El protocolo incluye operaciones para handshake, ping, canales, objetos, archivos, datos de jugador/canal, UDP y funciones reenviadas. Entre ellas están `RequestID`, `ResponseID`, `RequestJoinChannel`, `RequestCreateObject`, `RequestTransferObject`, `RequestDestroyObject`, `ForwardToAll` y `ForwardToOthers`.

La enumeración completa debe consultarse en `Comun/Paquete.cs` de Eco porque es la fuente normativa del protocolo actual. fileciteturn227file0

## Request y Response [#request-y-response]

Muchos intercambios siguen este patrón:

```text
Cliente ── RequestX ──► Servidor
Cliente ◄─ ResponseX ── Servidor
```

Otros mensajes son notificaciones o reenvíos y no tienen que seguir una pareja request/response clásica.

## Paquetes reenviados [#paquetes-reenviados]

Las operaciones `ForwardToAll`, `ForwardToOthers` y variantes persistentes permiten que el servidor reenvíe datos a participantes del canal. El protocolo mantiene el contexto del jugador emisor, canal y objeto.

## No mezclar protocolo y gameplay [#no-mezclar-protocolo-y-gameplay]

No diseñes un `Paquete` nuevo sólo porque necesites una nueva mecánica del juego. Primero determina si la mecánica encaja en una RFC, sincronización, datos de jugador/canal o una de las operaciones existentes. Los paquetes de protocolo deben ser la capa más estable.

## Debugging [#debugging]

Cuando una comunicación falla, inspecciona en este orden:

```text
1. ¿Se está generando la operación?
2. ¿Qué paquete representa esa operación?
3. ¿El buffer contiene los datos esperados?
4. ¿Se envió por el transporte correcto?
5. ¿El receptor procesa ese paquete?
```

## Referencias [#referencias]

<Card title="Buffer" href="/docs/red/v1/transporte/buffers">
  Contenedor binario que transporta los datos del paquete.
</Card>

<Card title="TCP y UDP" href="/docs/red/v1/transporte/tcp-udp">
  Elección y características del transporte.
</Card>
