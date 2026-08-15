# Serialización (/docs/red/v1/referencia/api/serializacion)



# Serialización [#serialización]

`Serializacion` contiene los conversores y extensiones que permiten representar tipos de C# en datos que Eco puede enviar o guardar.

Hay dos caminos principales:

```text
                Serialización
                 /         \
                /           \
           Binaria          Nodo
              │                │
        RFC / Paquetes    Persistencia / datos
```

## Serialización binaria [#serialización-binaria]

`IBinarySerializable` permite controlar manualmente cómo una clase se escribe en un `BinaryWriter` y se reconstruye desde un `BinaryReader`.

```csharp
public class EstadoUnidad : IBinarySerializable
{
    public int vida;
    public Vector3 posicion;

    public void Serialize(BinaryWriter writer)
    {
        writer.Write(vida);
        writer.Write(posicion);
    }

    public void Deserialize(BinaryReader reader)
    {
        vida = reader.ReadInt32();
        posicion = reader.Read<Vector3>();
    }
}
```

El objetivo habitual es reducir tamaño y coste frente a serializar automáticamente todos los campos de una clase.

## Serialización con Nodo [#serialización-con-nodo]

`IDataNodeSerializable` está orientado a representaciones jerárquicas:

```csharp
public void Serialize(Nodo node)
{
    node.SetChild("Vida", vida);
    node.SetChild("Posicion", posicion);
}
```

Se utiliza especialmente cuando los datos deben ser legibles, versionables o persistirse en una estructura jerárquica.

## Conversión de tipos [#conversión-de-tipos]

`Serializacion.Convert` permite adaptar valores cuando el dato almacenado no coincide exactamente con el tipo solicitado por el consumidor.

```csharp
int vida = Serializacion.Convert(valor, 100);
```

Esto también se utiliza internamente por `Nodo.Get<T>()` y `Nodo.GetHierarchy<T>()`.

## Tipos conocidos [#tipos-conocidos]

La implementación contiene soporte explícito para numerosos tipos básicos y estructuras de Unity, además de `Vector2D`, `Vector3D`, arrays, listas y `Nodo`.

No debes asumir que cualquier objeto arbitrario es serializable de la misma forma: las clases complejas se benefician de implementar interfaces propias o de utilizar datos simples.

## Compatibilidad de versiones [#compatibilidad-de-versiones]

Cuando un dato persistente pueda sobrevivir a actualizaciones del juego, evita depender de detalles frágiles de nombres de tipos internos. Prefiere estructuras de `Nodo` con rutas estables y valores versionables.

<Callout title="Datos persistentes" type="info">
  La serialización es parte del contrato de los datos. Cambiar un nombre, tipo o estructura puede hacer incompatibles partidas o mensajes existentes.
</Callout>

## Texto frente a binario [#texto-frente-a-binario]

| Formato      | Ventaja                       | Uso                          |
| ------------ | ----------------------------- | ---------------------------- |
| Nodo / texto | Más inspeccionable y flexible | Datos y herramientas         |
| Binario      | Compacto y rápido             | Red y persistencia eficiente |
| Comprimido   | Reduce tamaño                 | Archivos grandes             |

## `TypeToName` y `NameToType` [#typetoname-y-nametotype]

Eco mantiene una representación textual estable para distintos tipos:

```csharp
string nombre = Serializacion.TypeToName(typeof(Vector3));
Type tipo = Serializacion.NameToType(nombre);
```

Esto es relevante cuando la representación necesita sobrevivir fuera de la instancia de C# que la creó.

## Cuándo crear un serializer propio [#cuándo-crear-un-serializer-propio]

Implementa serialización propia cuando:

* el objeto se transmite con mucha frecuencia;
* el objeto contiene datos que pueden compactarse considerablemente;
* necesitas controlar compatibilidad entre versiones;
* necesitas evitar reflexión en una ruta crítica.

No la añadas sólo por reutilizar una clase una vez: el serializer también se convierte en una responsabilidad de mantenimiento.

<Card title="Nodo" href="/docs/red/v1/referencia/api/nodo">
  Estructura jerárquica utilizada para datos.
</Card>

<Card title="Buffer" href="/docs/red/v1/referencia/api/buffer">
  Contenedor binario de bajo nivel.
</Card>

<Card title="Serializacion.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Comun/Serializacion.cs">
  Implementación de la serialización actual.
</Card>
