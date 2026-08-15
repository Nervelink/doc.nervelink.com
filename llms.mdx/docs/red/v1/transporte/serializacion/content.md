# Serialización (/docs/red/v1/transporte/serializacion)



# Serialización [#serialización]

La serialización es la capa que transforma los datos que utiliza Eco en una representación que puede escribirse en un `Buffer`, almacenarse o enviarse por la red.

Eco distingue principalmente entre datos estructurados mediante `Nodo` y serialización binaria mediante `IBinarySerializable` y `Serializacion`.

<Callout title="Separar las capas" type="info">
  `Nodo` describe datos. `Buffer` contiene bytes. `Paquete` describe un mensaje del protocolo. No son tres nombres para la misma cosa.
</Callout>

## Nodo [#nodo]

`Nodo` es una estructura jerárquica formada por nombre, valor y nodos hijos. Permite representar datos con rutas y convertir valores entre tipos compatibles.

```text
Nodo
├── name
├── value
└── children
    ├── ...
    └── ...
```

Entre sus operaciones están `AddChild`, `GetChild`, `SetChild`, `GetHierarchy` y `SetHierarchy`.

## Serialización binaria [#serialización-binaria]

Las clases que necesitan controlar cómo se representan sus datos pueden implementar `IBinarySerializable`:

```csharp
public interface IBinarySerializable
{
    void Serialize(BinaryWriter writer);
    void Deserialize(BinaryReader reader);
}
```

Esto permite definir una representación específica cuando la serialización automática no sea suficiente o cuando sea importante reducir el tamaño de los datos.

## Serialización de tipos [#serialización-de-tipos]

`Serializacion` proporciona extensiones para escribir y leer tipos básicos, estructuras de Unity, arrays, listas y objetos compatibles con el sistema de Eco.

La resolución de tipos puede utilizar nombres serializados y cachés de tipo para evitar repetir el trabajo de reflexión.

## Nodo frente a binario [#nodo-frente-a-binario]

Los dos mecanismos responden a necesidades distintas:

| Necesidad                          | Mecanismo              |
| ---------------------------------- | ---------------------- |
| Datos jerárquicos y configurables  | `Nodo`                 |
| Mensajes compactos de red          | Serialización binaria  |
| Formato personalizado de una clase | `IBinarySerializable`  |
| Persistencia estructurada          | `Nodo` + serialización |

## Relación con Buffer [#relación-con-buffer]

El resultado de una serialización binaria termina normalmente en un `Buffer`:

```text
Objeto / dato
      ↓
Serializacion
      ↓
BinaryWriter
      ↓
Buffer
      ↓
Paquete
```

El `Buffer` no decide qué significa el dato. Sólo proporciona el soporte de lectura y escritura binaria.

## Relación con Paquete [#relación-con-paquete]

Un `Paquete` añade significado de protocolo al flujo binario. Por ejemplo, dos mensajes pueden transportar datos con la misma representación binaria pero utilizar diferentes tipos de paquete y, por tanto, tener comportamientos distintos.

## Relación con TNet [#relación-con-tnet]

Eco conserva el modelo de serialización heredado de TNet, incluyendo `DataNode`, `IBinarySerializable` y la infraestructura de escritura y lectura binaria. La nomenclatura de Eco utiliza `Nodo` y `Serializacion`.

## Referencias [#referencias]

<Card title="Eco" href="https://github.com/Nervelink/eco/tree/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Comun">
  Código de `Nodo`, `Buffer` y serialización.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream de la implementación heredada.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Referencia externa para contrastar el sistema de serialización de TNet.
</Card>
