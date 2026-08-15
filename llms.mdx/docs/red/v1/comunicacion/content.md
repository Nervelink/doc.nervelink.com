# Comunicación (/docs/red/v1/comunicacion)



# Comunicación [#comunicación]

La comunicación de Eco se apoya en dos niveles que conviene mantener separados: el protocolo de red, representado por `Paquete` y `Buffer`, y la comunicación de alto nivel asociada a `Objeto` y `Componente`.

<Callout title="Idea principal" type="info">
  Un `Paquete` transporta datos. Un `Objeto` da contexto de red a esos datos. Los mecanismos de llamadas remotas y sincronización determinan qué comportamiento se ejecuta y quién debe recibirlo.
</Callout>

## Capas de comunicación [#capas-de-comunicación]

```text
Componente / gameplay
        │
        ▼
      Objeto
        │
   RFC / datos
        │
        ▼
     Paquete
        │
     Buffer
        │
        ▼
   Transporte
        │
        ▼
      Canal
        │
        ▼
   Destinatarios
```

Estas capas no son intercambiables. El protocolo puede transportar mensajes sin conocer la lógica del juego, mientras que un objeto puede utilizar distintos tipos de mensajes para expresar acciones o cambios de estado.

## RFC [#rfc]

Eco conserva el concepto de **Remote Function Call** de TNet. Una RFC identifica una función asociada a un objeto y contiene los datos necesarios para ejecutarla remotamente.

En `Canal`, las RFC se almacenan con:

```text
RFC
├── uid
├── functionName
└── data
```

El `uid` contiene el identificador del objeto y el identificador compacto de la función. Cuando la función no utiliza un identificador numérico, se almacena también su nombre. El canal puede reconstruir el paquete correspondiente a partir de estos datos. fileciteturn63file0

## Destinatarios [#destinatarios]

Una llamada remota no implica necesariamente "enviar a todos". El sistema de objetos de Eco permite expresar distintos destinatarios y, en el caso de los datos persistentes, utilizar un recorrido diferente al de una comunicación efímera.

En la implementación de `Objeto`, por ejemplo, los cambios de datos pueden enviarse a `Objetivo.Otros`, al propietario del objeto o mediante objetivos persistentes según el caso. fileciteturn54file0

La documentación específica de `Objetivo` y de cada modo de RFC se desarrollará en las páginas de referencia de esta sección.

## RFC persistentes [#rfc-persistentes]

Una característica importante del modelo de Eco es que algunas comunicaciones pueden quedar asociadas al estado persistente del objeto o del canal.

`Canal` conserva las RFC asociadas a objetos dinámicos y puede exportarlas junto con los datos de creación del objeto. Al importar un objeto, el canal restaura sus datos y vuelve a distribuir las RFC guardadas. fileciteturn63file0

Esto significa que una RFC persistente no debe entenderse simplemente como un mensaje que llega más tarde. Puede formar parte del estado necesario para reconstruir el estado de red de un objeto.

## Comunicación a través de `Componente` [#comunicación-a-través-de-componente]

La clase `Componente` está diseñada para que un comportamiento de Unity pueda trabajar con su `Objeto` asociado sin tener que resolverlo manualmente cada vez.

```csharp
public class MiUnidad : Componente
{
    public void Atacar()
    {
        ero.Send("Atacar", Objetivo.Otros);
    }
}
```

El patrón es:

```text
Componente
   ↓
ero
Objeto
   ↓
Send / RFC
   ↓
Paquete
```

`Componente` también proporciona `Instantiate`, `DestroySelf`, `Get` y `Set` como accesos de conveniencia sobre el objeto de red. fileciteturn60file0

## El canal como contexto [#el-canal-como-contexto]

Las RFC almacenadas por Eco pertenecen al canal. Una entrada de RFC incluye el identificador del objeto, y el paquete que la representa incluye también el identificador del canal. fileciteturn62file0

Esto evita tratar una función remota como un mensaje global independiente del estado de sesión:

```text
Canal A
 ├── Objeto 10
 │    └── RFC
 └── Objeto 20
      └── RFC

Canal B
 └── Objeto 10
      └── RFC diferente
```

El mismo identificador de objeto puede tener sentido dentro del contexto de un canal diferente, por lo que el contexto del canal forma parte de la dirección efectiva del mensaje.

## Paquetes y buffers [#paquetes-y-buffers]

`ClienteJuego` crea los paquetes mediante `CrearPaquete` y los finaliza mediante `EnviarPaquete`. Según el tipo de comunicación, el envío puede utilizar TCP o UDP; la selección de transporte se realiza por debajo de esta capa. fileciteturn26file0

```text
CrearPaquete
    ↓
Escribir datos
    ↓
EnviarPaquete
    ↓
TCP / UDP
```

Esto permite que la capa de gameplay trabaje con objetos y comandos sin tener que construir directamente las estructuras de sockets.

## Cliente y servidor [#cliente-y-servidor]

En el cliente, `ClienteJuego` mantiene la cola y el procesamiento de paquetes y conoce los canales en los que participa. En el servidor, `ServidorJuego` mantiene los jugadores y canales activos y procesa las operaciones de red. fileciteturn23file0 fileciteturn36file0

El flujo conceptual es:

```text
Cliente
  │
  │ Paquete
  ▼
Servidor
  │
  ├── valida / procesa
  ├── determina canal
  └── determina destinatarios
       │
       ▼
   otros clientes
```

## Comunicación y estado [#comunicación-y-estado]

No todo lo que viaja por la red debe modelarse como una RFC.

Una forma útil de separar conceptos es:

| Necesidad                                | Mecanismo conceptual                 |
| ---------------------------------------- | ------------------------------------ |
| Ejecutar comportamiento remoto           | RFC                                  |
| Restaurar comportamiento/estado guardado | RFC persistente / estado del canal   |
| Actualizar datos de un objeto            | `Set` / datos del objeto             |
| Mover datos binarios de protocolo        | `Paquete` + `Buffer`                 |
| Sincronizar un estado continuamente      | Sistema de sincronización específico |

La elección concreta entre RFC, datos y sincronización debe hacerse según la semántica del dato, no simplemente porque RFC sea el mecanismo más visible.

## Referencia a TNet [#referencia-a-tnet]

Eco mantiene aquí la arquitectura heredada de TNet, pero la nomenclatura pública se ha adaptado:

| TNet          | Eco          |
| ------------- | ------------ |
| `TNObject`    | `Objeto`     |
| `TNBehaviour` | `Componente` |
| `TNPacket`    | `Paquete`    |
| `TNBuffer`    | `Buffer`     |
| `TNChannel`   | `Canal`      |
| RFC           | RFC          |

La documentación de TNet es útil para entender el diseño original y localizar conceptos equivalentes, pero la referencia normativa para Eco es el repositorio `Nervelink/eco`.

## Referencias [#referencias]

<Card title="Eco" href="https://github.com/Nervelink/eco">
  Código fuente de la implementación actual de Eco.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream del que procede la base de Eco.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Documentación generada del repositorio actual de TNet para contrastar arquitectura y evolución upstream.
</Card>
