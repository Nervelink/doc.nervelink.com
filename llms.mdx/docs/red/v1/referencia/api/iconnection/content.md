# IConnection (/docs/red/v1/referencia/api/iconnection)



# IConnection [#iconnection]

`IConnection` separa el modelo de red de Eco de la implementación física de la conexión. Esta abstracción permite que el runtime utilice el transporte TCP/UDP habitual o una integración externa compatible.

```text
Eco
 ↓
ClienteJuego
 ↓
IConnection
 ├── TCP / sockets
 ├── Steam Networking
 └── transporte personalizado
```

## Cuándo utilizarla [#cuándo-utilizarla]

No necesitas implementar `IConnection` para un juego normal. Es apropiado cuando:

* la plataforma ofrece su propio transporte;
* necesitas reemplazar sockets por una API externa;
* estás creando una integración específica de servidor;
* quieres mantener el protocolo de Eco y cambiar sólo el canal físico de transporte.

<Callout title="Mantén separado el protocolo" type="info">
  Una conexión personalizada debe transportar los datos de Eco; no debería duplicar `Canal`, `Objeto`, ownership o sincronización. Esas responsabilidades siguen perteneciendo al runtime de Eco.
</Callout>

## Responsabilidades [#responsabilidades]

Una implementación de conexión debe encargarse de aspectos como:

```text
Conectar
Desconectar
Enviar bytes
Recibir bytes
Estado de conexión
Dirección / identidad del peer
```

El cliente sigue interpretando los bytes mediante `Paquete` y `Buffer`.

## Steam Networking [#steam-networking]

La arquitectura permite integrar Steam Networking como transporte alternativo. El juego conserva el mismo modelo de canales, jugadores y objetos, mientras `IConnection` sustituye la ruta de sockets.

```text
Gameplay
   ↓
Eco
   ↓
ClienteJuego
   ↓
IConnection
   ↓
Steam Networking
```

Esto resulta especialmente útil cuando una plataforma proporciona NAT traversal, identidad o routing propio.

## Error habitual [#error-habitual]

No mezcles una implementación de `IConnection` con lógica de gameplay:

```text
Incorrecto
IConnection
 ├── conectar
 ├── enviar bytes
 └── crear enemigos
```

La separación correcta es:

```text
IConnection
 └── transporte

Eco / ClienteJuego
 └── protocolo y sesión

Objeto / Componente
 └── gameplay
```

## Diagnóstico [#diagnóstico]

Cuando una conexión personalizada falla, comprueba por capas:

1. conexión física establecida;
2. bytes enviados y recibidos;
3. handshake `RequestID` / `ResponseID`;
4. creación de `ClienteJuego`;
5. entrada en canales;
6. procesamiento normal de paquetes.

Si el handshake no funciona, todavía no tiene sentido investigar objetos o RFC.

## Referencias [#referencias]

<Card title="Transportes personalizados" href="/docs/red/v1/avanzado/transportes-personalizados">
  Integración de una conexión distinta del transporte estándar.
</Card>

<Card title="Steam y conexiones" href="/docs/red/v1/avanzado/steam-y-conexiones">
  Caso de uso de una plataforma externa.
</Card>

<Card title="ClienteJuego" href="/docs/red/v1/referencia/api/cliente-juego">
  Consumidor principal de la abstracción de conexión.
</Card>
