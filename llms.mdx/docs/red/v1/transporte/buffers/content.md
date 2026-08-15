# Buffers (/docs/red/v1/transporte/buffers)



# Buffers [#buffers]

`Buffer` combina un flujo de memoria con las operaciones de `BinaryWriter` y `BinaryReader`. Es la pieza que Eco utiliza para construir, leer, copiar y reciclar datos binarios.

```text
Dato
 ↓
Buffer
 ├── escribir
 ├── leer
 └── reciclar
 ↓
Paquete
```

## Escritura y lectura [#escritura-y-lectura]

El ciclo básico es:

```csharp
var buffer = Buffer.Create();
var writer = buffer.BeginWriting();
writer.Write(123);
writer.Write("Hola");
buffer.EndWriting();

var reader = buffer.BeginReading();
int numero = reader.ReadInt32();
string texto = reader.ReadString();
```

El buffer controla internamente la posición y el tamaño útil de los datos.

## Modos del buffer [#modos-del-buffer]

`BeginWriting` prepara el buffer para escribir y `EndWriting` finaliza esa fase. `BeginReading` cambia al modo de lectura y permite consumir los bytes existentes.

Esto permite reutilizar la misma abstracción tanto para paquetes salientes como para datos recibidos.

## Tamaño y posición [#tamaño-y-posición]

Las propiedades principales son:

| Propiedad   | Significado                            |
| ----------- | -------------------------------------- |
| `size`      | Cantidad de datos útiles.              |
| `position`  | Posición actual del flujo.             |
| `stream`    | `MemoryStream` subyacente.             |
| `buffer`    | Array interno que contiene la memoria. |
| `isWriting` | Indica si está en modo escritura.      |

## Copia [#copia]

`CopyTo` permite copiar el contenido útil de un buffer a otro, evitando arrastrar espacio no utilizado del `MemoryStream`.

## Reciclado [#reciclado]

Eco puede reutilizar buffers para reducir asignaciones. `Recycle()` devuelve el buffer al pool cuando el reciclado está habilitado.

```text
Crear
 ↓
usar
 ↓
Recycle
 ↓
pool
 ↓
Create
 ↓
reutilizar
```

El código también incorpora comprobaciones de depuración para detectar usos incorrectos y dobles reciclados cuando las opciones correspondientes están habilitadas.

## Seguridad del reciclado [#seguridad-del-reciclado]

Un buffer reciclado no debe utilizarse posteriormente como si siguiera siendo válido. Las rutas de depuración de Eco permiten detectar esta clase de error.

Por eso las funciones que reciben un `Buffer` deben dejar claro quién posee su ciclo de vida.

## Buffer y Datagramas [#buffer-y-datagramas]

El mismo sistema de reciclado puede utilizarse con `Datagrama`, evitando crear memoria constantemente durante tráfico frecuente.

## Relación con paquetes [#relación-con-paquetes]

Un `Paquete` define el significado de los bytes; un `Buffer` proporciona el espacio y las operaciones para escribirlos.

```text
Paquete
   │
   └── BeginPacket()
           ↓
        Buffer
           ↓
       BinaryWriter
```

## Relación con TNet [#relación-con-tnet]

`Buffer` es la evolución renombrada de `TNBuffer`. La arquitectura de pool, lectura/escritura y reciclado procede de TNet, mientras que la nomenclatura de Eco es la API utilizada por Pandora.

<Card title="Buffer.cs · Eco" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Comun/Buffer.cs">
  Implementación del buffer binario.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream.
</Card>
