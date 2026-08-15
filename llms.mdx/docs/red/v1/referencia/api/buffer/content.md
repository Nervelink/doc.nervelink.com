# Buffer (/docs/red/v1/referencia/api/buffer)



# Buffer [#buffer]

`Buffer` es la estructura binaria que Eco utiliza para construir y leer mensajes de red. Se encuentra debajo de `Objeto`, `Componente` y RFC, pero por encima del transporte físico.

```text
Gameplay
   ↓
RFC / estado
   ↓
Paquete
   ↓
Buffer
   ↓
TCP / UDP / conexión personalizada
```

## Para qué sirve [#para-qué-sirve]

Un `Buffer` mantiene los bytes y la posición de lectura/escritura necesarios para serializar parámetros, identificadores y datos de protocolo.

En el uso normal no necesitas construir buffers manualmente.

<Callout title="API avanzada" type="warn">
  Manipular `Buffer` directamente significa asumir responsabilidad por el orden, tipos y longitud exacta de los datos. Utilízalo para protocolos personalizados, herramientas de red y extensiones del runtime.
</Callout>

## Escritura [#escritura]

El buffer dispone de operaciones especializadas para los tipos soportados por `Serializacion` y por el protocolo de Eco.

```csharp
var buffer = new Buffer();
buffer.Write(42);
buffer.Write("Arena");
buffer.Write(Vector3.zero);
```

El ejemplo conceptual anterior representa el patrón; para paquetes reales debes utilizar los helpers proporcionados por la implementación del protocolo.

## Lectura [#lectura]

La lectura consume los datos en el mismo orden en que fueron escritos:

```csharp
int id = buffer.Read<int>();
string nombre = buffer.Read<string>();
Vector3 posicion = buffer.Read<Vector3>();
```

<Callout title="El orden importa" type="error">
  Un paquete que escriba `int → string → Vector3` debe leerse exactamente en ese orden. Leer un tipo diferente desplaza el cursor y puede corromper el resto del mensaje.
</Callout>

## Arrays y bloques [#arrays-y-bloques]

Eco incluye operaciones para escribir y leer arrays y bloques de bytes. Para mensajes grandes, conserva la longitud y evita crear copias innecesarias cuando el protocolo ya conoce el tamaño.

## Buffer reutilizable [#buffer-reutilizable]

El runtime utiliza buffers en numerosas rutas de red para reducir asignaciones. Por ese motivo, una extensión que retenga una instancia más tiempo del necesario puede provocar problemas difíciles de diagnosticar.

```text
Recibir
  ↓
Leer
  ↓
Procesar
  ↓
Liberar / reutilizar
```

## Buffer frente a Nodo [#buffer-frente-a-nodo]

| Tipo            | Uso                                     |
| --------------- | --------------------------------------- |
| `Nodo`          | Datos estructurados y persistencia      |
| `Buffer`        | Datos binarios del protocolo            |
| `Paquete`       | Significado de un mensaje               |
| `Serializacion` | Conversión entre tipos y representación |

## Diagnóstico [#diagnóstico]

Cuando un paquete falla durante la lectura, comprueba primero:

1. que el identificador de `Paquete` sea el esperado;
2. que escritor y lector utilicen el mismo orden;
3. que las longitudes de arrays sean correctas;
4. que el transporte no haya entregado un mensaje distinto al handler esperado.

<Card title="Paquete" href="/docs/red/v1/referencia/api/paquete">
  Tipos de mensajes que utilizan el buffer.
</Card>

<Card title="Serialización" href="/docs/red/v1/referencia/api/serializacion">
  Conversión de valores antes de escribirlos en el buffer.
</Card>

<Card title="Buffer.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Comun/Buffer.cs">
  Implementación actual.
</Card>
