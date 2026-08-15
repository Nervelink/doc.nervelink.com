# Serialización (/docs/red/v1/serializacion)



# Serialización [#serialización]

La serialización es la capa que convierte los datos utilizados por Eco en una representación binaria que puede viajar por la red, y permite reconstruirlos al recibirlos.

<Callout title="Tres capas distintas" type="info">
  `Nodo` representa datos estructurados, `Buffer` representa bytes en memoria y `Paquete` identifica el significado de un mensaje del protocolo. No son tres nombres para la misma cosa.
</Callout>

## Modelo general [#modelo-general]

```text
Dato / estado
    │
    ├── Nodo
    │     └── estructura jerárquica
    │
    └── objeto binario
           │
           ▼
        Buffer
           │
           ▼
        Paquete
           │
           ▼
      TCP / UDP
```

La capa superior decide qué información debe enviarse. La serialización decide cómo representarla. El transporte se ocupa después de mover los bytes.

## `Nodo` [#nodo]

`Nodo` es la estructura de datos jerárquica de Eco. Cada nodo tiene un nombre, un valor opcional y una colección de nodos hijos.

```text
Nodo
├── name
├── value
└── children
    ├── Nodo
    ├── Nodo
    └── ...
```

Esto permite representar datos simples:

```csharp
var nodo = new Nodo("Jugador", "Nineplus");
```

y estructuras más profundas:

```text
Jugador
├── Nombre
├── Estadisticas
│   ├── Vida
│   └── Defensa
└── Configuracion
    └── Calidad
```

`Nodo` también soporta acceso jerárquico mediante rutas como `Estadisticas/Vida`, además de conversión de tipos mediante `Get<T>()`. fileciteturn71file0

## `Nodo` como estado compartido [#nodo-como-estado-compartido]

Los objetos de Eco utilizan `Nodo` para almacenar datos que pueden sincronizarse:

```csharp
objeto.Set("Vida", 100);
int vida = objeto.Get<int>("Vida");
```

En este contexto el nodo representa el estado, mientras que la capa de comunicación decide cómo se propaga ese cambio.

La documentación de sincronización explica la diferencia entre modificar un `Nodo` localmente y sincronizarlo con otros clientes.

## Serialización binaria [#serialización-binaria]

Eco utiliza `BinaryReader` y `BinaryWriter` a través de la clase `Buffer`. Además existe la interfaz `IBinarySerializable` para tipos que necesitan controlar explícitamente su representación binaria.

```csharp
public interface IBinarySerializable
{
    void Serialize(BinaryWriter writer);
    void Deserialize(BinaryReader reader);
}
```

Implementar esta interfaz permite elegir exactamente qué bytes se escriben y leen y puede resultar útil para reducir el tamaño de mensajes frecuentes. La propia implementación de `Serializacion` señala este patrón como la vía adecuada cuando se desea una serialización más eficiente. fileciteturn74file0

## `Buffer` [#buffer]

`Buffer` combina las funciones de lectura y escritura binarias y utiliza un `MemoryStream` como almacenamiento interno. Eco puede crear un buffer, escribir los datos y después volver a leerlos desde el principio.

```csharp
var buffer = Buffer.Create();
var writer = buffer.BeginWriting();
writer.Write(123);
writer.Write("Hola");
buffer.EndWriting();

var reader = buffer.BeginReading();
int id = reader.ReadInt32();
string texto = reader.ReadString();
```

El buffer también expone su posición, tamaño, stream subyacente y acceso al almacenamiento binario. fileciteturn72file0

## Ciclo de escritura y lectura [#ciclo-de-escritura-y-lectura]

El flujo normal es:

```text
Buffer.Create()
     ↓
BeginWriting()
     ↓
Write(...)
     ↓
EndWriting()
     ↓
[transporte / almacenamiento]
     ↓
BeginReading()
     ↓
Read(...)
```

No se debe leer un buffer que todavía esté en modo de escritura. `EndWriting()` finaliza la escritura y prepara el stream para ser leído.

## Reutilización de buffers [#reutilización-de-buffers]

La implementación incorpora un sistema de pool que puede reciclar buffers cuando `RECYCLE_BUFFERS` está habilitado. `Create()` puede recuperar una instancia ya existente y `Recycle()` devuelve el objeto al pool. fileciteturn72file0

Esto está pensado para reducir asignaciones temporales producidas por el tráfico de red.

```text
Crear
 ↓
Usar
 ↓
Enviar / procesar
 ↓
Recycle
 ↓
Pool
 ↓
Reutilizar
```

Cuando se reutilizan buffers, la propiedad del buffer debe respetarse estrictamente: después de `Recycle()` no debe seguir utilizándose.

## `Paquete` [#paquete]

`Paquete` es un `enum` que identifica el significado de un mensaje dentro del protocolo.

Por ejemplo:

```text
RequestID
ResponseID
RequestJoinChannel
ResponseJoinChannel
RequestCreateObject
ResponseCreateObject
ForwardToOthers
...
```

El paquete no contiene por sí solo todos los datos del mensaje. Identifica el tipo y el resto de la información se codifica dentro del `Buffer`. fileciteturn73file0

Por eso el modelo es:

```text
Paquete = qué significa
Buffer   = qué bytes contiene
```

## Construcción de un paquete [#construcción-de-un-paquete]

`ClienteJuego` expone `CrearPaquete` para inicializar un `Buffer` con el tipo de paquete correspondiente.

```csharp
var buffer = Eco.CrearPaquete(Paquete.RequestPing);
Eco.EnviarPaquete(buffer);
```

Cuando existen datos adicionales:

```csharp
var buffer = Eco.CrearPaquete(Paquete.RequestSetPlayerData);
var writer = buffer.writer;
writer.Write(playerID);
writer.Write("Vida");
writer.WriteObject(100);
Eco.EnviarPaquete(buffer);
```

El uso de `CrearPaquete` mantiene la construcción del mensaje alineada con el protocolo de Eco. fileciteturn26file0

## Serialización de objetos [#serialización-de-objetos]

La clase `Serializacion` proporciona métodos para escribir y leer tipos básicos y tipos conocidos por Unity, además de estructuras de Eco como `Nodo`. También puede resolver tipos a partir de nombres serializados y convertir valores a tipos compatibles. fileciteturn74file0

Para clases personalizadas existe una diferencia importante:

| Necesidad                                | Enfoque                 |
| ---------------------------------------- | ----------------------- |
| Serialización binaria genérica           | `Serializacion`         |
| Controlar exactamente el formato binario | `IBinarySerializable`   |
| Datos estructurados jerárquicos          | `Nodo`                  |
| Datos jerárquicos personalizados         | `IDataNodeSerializable` |
| Mensaje del protocolo                    | `Paquete` + `Buffer`    |

## `IDataNodeSerializable` [#idatanodeserializable]

Cuando un tipo necesita controlar cómo se representa dentro de `Nodo`, puede implementar `IDataNodeSerializable`:

```csharp
public interface IDataNodeSerializable
{
    void Serialize(Nodo nodo);
    void Deserialize(Nodo nodo);
}
```

Esto permite definir una representación estructurada sin depender de una reflexión genérica para cada campo. fileciteturn71file0

## Datos y protocolo [#datos-y-protocolo]

Es importante no confundir serialización de datos con serialización de protocolo.

```text
Objeto
  │
  ├── estado → Nodo
  │
  └── acción → RFC / datos
                │
                ▼
             Paquete
                │
                ▼
              Buffer
```

Un `Nodo` puede contener datos de gameplay, mientras que `Paquete` describe una operación de red como entrar en un canal, crear un objeto o enviar una llamada remota.

## Rendimiento [#rendimiento]

Las estructuras de serialización están diseñadas para evitar trabajo innecesario en el camino crítico de red. `Buffer` evita crear un `BinaryWriter` y `BinaryReader` separados por mensaje, y el pool opcional permite reutilizar buffers. `IBinarySerializable` permite evitar formatos más pesados cuando una estructura necesita una codificación compacta. fileciteturn72file0 fileciteturn74file0

En mensajes frecuentes, la serialización debe tratarse como parte del coste de red: no basta con que el código sea correcto; el tamaño del mensaje y las asignaciones también importan.

## Referencia a TNet [#referencia-a-tnet]

La arquitectura de serialización de Eco procede directamente de la familia de tipos de TNet y mantiene el mismo modelo general:

| TNet                    | Eco                     |
| ----------------------- | ----------------------- |
| `DataNode`              | `Nodo`                  |
| `TNBuffer` / `Buffer`   | `Buffer`                |
| `TNPacket` / `Packet`   | `Paquete`               |
| `IBinarySerializable`   | `IBinarySerializable`   |
| `IDataNodeSerializable` | `IDataNodeSerializable` |
| `TNet.Serialization`    | `Serializacion`         |

La correspondencia sirve para consultar el código upstream. Para implementar Eco debe tomarse como referencia el código de `Nervelink/eco`.

## Referencias [#referencias]

<Card title="Eco" href="https://github.com/Nervelink/eco">
  Implementación actual de las capas de datos, serialización y protocolo.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream del que procede esta arquitectura.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Documentación generada sobre el repositorio actual de TNet para contrastar arquitectura y evolución.
</Card>
