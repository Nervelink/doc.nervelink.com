# Paquetes (/docs/red/v1/transporte/paquetes)



# Paquetes [#paquetes]

`Paquete` identifica el tipo de operación que está viajando por la red. El contenido binario del mensaje se escribe mediante `Buffer` y su significado depende del tipo de paquete.

```text
Operación
   ↓
Paquete
   ↓
Buffer
   ↓
TCP / UDP
```

## Tipos de paquete [#tipos-de-paquete]

Eco conserva un conjunto de paquetes para operaciones como conexión, ping, canales, creación y destrucción de objetos, datos de jugador y forwarding de llamadas remotas.

Algunos ejemplos son:

| Paquete                | Propósito                                               |
| ---------------------- | ------------------------------------------------------- |
| `RequestID`            | Iniciar la identificación y negociación de la conexión. |
| `RequestPing`          | Medir latencia y mantener la conexión activa.           |
| `RequestJoinChannel`   | Solicitar entrada a un canal.                           |
| `RequestLeaveChannel`  | Abandonar un canal.                                     |
| `RequestCreateObject`  | Crear un objeto dinámico.                               |
| `RequestDestroyObject` | Destruir un objeto de red.                              |
| `ForwardToAll`         | Distribuir una llamada o mensaje al canal.              |
| `ForwardToOthers`      | Distribuirlo a los demás participantes.                 |

La enumeración completa vive en `Paquete.cs` y constituye parte del protocolo interno de Eco.

## Request y Response [#request-y-response]

Muchos mensajes están organizados como una pareja solicitud/respuesta:

```text
Cliente
  │
  │ Request...
  ▼
Servidor
  │
  │ Response...
  ▼
Cliente
```

No todos los paquetes siguen exactamente este patrón. Algunos son notificaciones, forwarding o mensajes internos de transporte.

## Canal dentro del protocolo [#canal-dentro-del-protocolo]

Muchas operaciones contienen el identificador de canal porque el servidor necesita conocer el ámbito en el que debe realizar la operación.

Esto permite que una misma conexión participe en varios canales sin mezclar sus estados.

## Objetos y paquetes [#objetos-y-paquetes]

Las operaciones relacionadas con objetos incluyen habitualmente un identificador de objeto. En RFC, el identificador puede combinar el objeto con un identificador compacto de función.

```text
UID
├── Object ID
└── Function ID
```

Cuando no existe un identificador numérico para la función, Eco puede transportar el nombre de la función.

## Fiabilidad [#fiabilidad]

Los paquetes se escriben sobre buffers y después se envían mediante las rutas fiables o rápidas que correspondan. La elección de TCP o UDP no cambia el significado del paquete: cambia cómo se transporta.

## Paquetes personalizados [#paquetes-personalizados]

El servidor y el cliente pueden exponer manejadores para paquetes que no formen parte del procesamiento estándar. Esto permite extender el protocolo cuando una aplicación necesita una operación específica.

Los paquetes personalizados deben mantener una definición clara del formato binario y de quién es responsable de procesarlos.

## Relación con TNet [#relación-con-tnet]

`Paquete` corresponde conceptualmente al sistema de `Packet`/`TNPacket` de TNet. La enumeración y los nombres de Eco son la referencia válida para este proyecto.

<Card title="Paquete.cs · Eco" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Comun/Paquete.cs">
  Implementación actual de los tipos de paquete.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Referencia para contrastar el protocolo heredado.
</Card>
