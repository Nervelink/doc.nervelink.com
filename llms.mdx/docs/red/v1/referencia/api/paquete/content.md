# Paquete (/docs/red/v1/referencia/api/paquete)



# Paquete [#paquete]

`Paquete` identifica el tipo de mensaje que viaja entre cliente, servidor y sistemas auxiliares. No representa los bytes por sí mismo: describe qué significan esos bytes.

```text
Paquete
   ↓
Buffer con parámetros
   ↓
Transporte
```

## Familias principales [#familias-principales]

### Conexión [#conexión]

Incluye mensajes como `RequestID`, `ResponseID`, `Disconnect`, `RequestPing` y `ResponsePing`.

### Canales [#canales]

Incluye entradas, salidas, listas, límites, carga de nivel y estado del canal:

```text
RequestJoinChannel
RequestLeaveChannel
RequestChannelList
RequestSetPlayerLimit
RequestLoadLevel
```

### Objetos [#objetos]

Gestiona creación, transferencia y destrucción:

```text
RequestCreateObject
ResponseCreateObject
RequestTransferObject
ResponseTransferObject
RequestDestroyObject
ResponseDestroyObject
```

### Archivos [#archivos]

Eco incluye mensajes para enumerar, guardar, cargar y eliminar archivos del servidor.

### Reenvío [#reenvío]

Los paquetes `ForwardToAll`, `ForwardToAllSaved` y `ForwardToOthers` permiten reenviar operaciones de objeto o RFC según el destinatario y persistencia necesarios.

## Handshake [#handshake]

El primer flujo de una conexión utiliza `RequestID` y `ResponseID` para negociar la versión de protocolo y asignar la identidad del jugador.

```text
Cliente
  │ RequestID
  ▼
Servidor
  │ ResponseID
  ▼
Cliente listo
```

Una incompatibilidad de protocolo puede terminar la conexión antes de entrar en un canal.

## UDP [#udp]

El establecimiento de UDP se realiza mediante `RequestSetUDP`, `ResponseSetUDP` y `RequestActivateUDP`.

```text
TCP
 ↓
Negociar puerto UDP
 ↓
Activar UDP
 ↓
Tráfico no fiable cuando corresponda
```

## Paquetes personalizados [#paquetes-personalizados]

Los paquetes reservados para extensiones permiten integrar protocolos de herramientas o transportes específicos. El receptor debe registrar el handler correspondiente antes de utilizarlos.

<Callout title="No confundas Paquete con RFC" type="info">
  Una RFC es una operación de alto nivel asociada a un objeto. Un `Paquete` es una unidad del protocolo que también puede representar conexión, canal, archivos, UDP, reenvíos y otras operaciones internas.
</Callout>

## Cómo diagnosticar un paquete [#cómo-diagnosticar-un-paquete]

Cuando investigues un problema de red, registra en este orden:

```text
Tipo de Paquete
      ↓
Canal / objeto / jugador
      ↓
Longitud del Buffer
      ↓
Contenido serializado
      ↓
Handler receptor
```

## Código fuente [#código-fuente]

<Card title="Paquete.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Comun/Paquete.cs">
  Catálogo actual del protocolo.
</Card>

<Card title="Buffer" href="/docs/red/v1/referencia/api/buffer">
  Cómo se leen y escriben los parámetros del paquete.
</Card>
