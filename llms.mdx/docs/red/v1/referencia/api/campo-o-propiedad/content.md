# CampoOPropiedad (/docs/red/v1/referencia/api/campo-o-propiedad)



# CampoOPropiedad [#campoopropiedad]

`CampoOPropiedad` encapsula un `FieldInfo` o un `PropertyInfo` y ofrece una interfaz común para inspeccionar y modificar miembros de una instancia.

> Es una utilidad de infraestructura. El código de gameplay normal no necesita utilizarla directamente.

## Para qué existe [#para-qué-existe]

Eco necesita resolver miembros de tipos dinámicamente en varias partes de su infraestructura, especialmente cuando la información llega desde estructuras serializadas o código que debe trabajar con tipos desconocidos en compilación.

```text
Tipo C#
  ↓
Reflection
  ↓
CampoOPropiedad
  ├── FieldInfo
  └── PropertyInfo
```

## Lectura [#lectura]

```csharp
var miembro = objeto.GetFieldOrProperty("vida");
int vida = miembro.GetValue<int>(objeto);
```

También existe el acceso directo mediante las extensiones:

```csharp
int vida = objeto.GetFieldOrPropertyValue<int>("vida");
```

## Escritura [#escritura]

```csharp
objeto.SetFieldOrPropertyValue("vida", 100);
```

`CampoOPropiedad` comprueba si el miembro es escribible antes de modificarlo y utiliza `Serializacion.ConvertObject` para convertir el valor al tipo esperado cuando sea posible.

## Metadatos [#metadatos]

La abstracción expone:

| Propiedad  | Significado                           |
| ---------- | ------------------------------------- |
| `name`     | Nombre del campo o propiedad.         |
| `canWrite` | Indica si puede modificarse.          |
| `type`     | Tipo declarado del miembro.           |
| `field`    | `FieldInfo` subyacente, si existe.    |
| `property` | `PropertyInfo` subyacente, si existe. |

También permite consultar atributos:

```csharp
bool serializable = miembro.HasAttribute<MiAtributo>();
MiAtributo atributo = miembro.GetAttribute<MiAtributo>();
```

## Caché de reflexión [#caché-de-reflexión]

Las extensiones `GetFieldOrProperty` mantienen una caché por tipo y nombre. Esto evita repetir la búsqueda por reflexión cada vez que una ruta dinámica necesita acceder al mismo miembro.

```text
Primera consulta
    ↓
Reflection
    ↓
CampoOPropiedad
    ↓
Cache[type][name]

Consultas posteriores
    ↓
Cache
```

<Callout title="No es una optimización gratuita" type="warn">
  La reflexión sigue teniendo un coste y la caché consume memoria. No conviene sustituir accesos directos de gameplay por reflexión simplemente porque `CampoOPropiedad` exista.
</Callout>

## Referencias [#referencias]

<Card title="Serialización" href="/docs/red/v1/referencia/api/serializacion">
  Conversión de valores utilizada por la infraestructura de reflexión.
</Card>

<Card title="Código fuente" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Comun/CampoOPropiedad.cs">
  Implementación actual de `CampoOPropiedad`.
</Card>
