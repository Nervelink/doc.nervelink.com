# Buffer en profundidad (/docs/red/v1/transporte/buffer-en-profundidad)



# Buffer en profundidad [#buffer-en-profundidad]

`Buffer` combina un `MemoryStream`, `BinaryWriter` y `BinaryReader` para representar un bloque binario que puede cambiar entre modo escritura y lectura. Eco lo utiliza como una de las piezas centrales del protocolo. fileciteturn228file0

## Ciclo de vida [#ciclo-de-vida]

```text
Buffer.Create()
    ↓
BeginWriting()
    ↓
writer.Write(...)
    ↓
EndWriting()
    ↓
BeginReading()
    ↓
reader.Read(...)
    ↓
Recycle()
```

No conviene mantener referencias a un buffer después de devolverlo al pool.

## Escritura [#escritura]

```csharp
Buffer buffer = Buffer.Create();
var writer = buffer.BeginWriting();
writer.Write(42);
writer.Write("Eco");
buffer.EndWriting();
```

`EndWriting()` fija el tamaño escrito y devuelve la posición de lectura al inicio.

## Lectura [#lectura]

```csharp
var reader = buffer.BeginReading();
int id = reader.ReadInt32();
string nombre = reader.ReadString();
```

La secuencia de lectura debe coincidir exactamente con la secuencia de escritura.

## Reciclado [#reciclado]

Eco puede compilar `Buffer` con reciclado habilitado. En ese modo `Recycle()` limpia el buffer y lo devuelve a un pool reutilizable; el código incluye además contadores y mecanismos de diagnóstico para detectar usos incorrectos. fileciteturn228file0

El pool tiene un límite y también existe `ReleaseUnusedMemory()` para liberar la memoria retenida por buffers que ya no se utilizan.

## Thread safety [#thread-safety]

`Recycle(bool threadSafe = true)` permite proteger el acceso al pool cuando el buffer se libera desde diferentes hilos. No significa que todas las operaciones del buffer sean automáticamente seguras para acceso concurrente.

## Rendimiento [#rendimiento]

La reutilización evita asignaciones repetidas de `MemoryStream` y estructuras asociadas, pero no debe utilizarse como excusa para mantener buffers enormes vivos indefinidamente. `Clear()` puede conservar o reemplazar el stream dependiendo de su capacidad. fileciteturn228file0

## Reglas prácticas [#reglas-prácticas]

1. El productor conoce cuándo ha terminado de escribir.
2. El consumidor no debe cambiar el contenido durante la lectura.
3. El buffer se recicla sólo cuando todos sus consumidores han terminado.
4. El protocolo debe escribir y leer en el mismo orden y con los mismos tipos.

## Referencias [#referencias]

<Card title="Paquetes y protocolo" href="/docs/red/v1/transporte/paquetes-protocolo">
  Cómo encaja `Buffer` dentro del protocolo.
</Card>

<Card title="Serialización" href="/docs/red/v1/transporte/serializacion">
  Tipos de datos y conversiones utilizadas por Eco.
</Card>
