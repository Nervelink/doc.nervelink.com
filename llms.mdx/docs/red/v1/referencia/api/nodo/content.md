# Nodo (/docs/red/v1/referencia/api/nodo)



# Nodo [#nodo]

`Nodo` es la estructura jerárquica de datos que Eco utiliza para representar información estructurada sin depender de una clase concreta de gameplay.

Su modelo es sencillo: un nodo tiene un nombre, un valor opcional y una colección de nodos hijos.

```text
Nodo
├── Nombre
├── Valor
└── Hijos
    ├── Perfil
    │   ├── Nivel
    │   └── Clase
    └── Estado
        └── Vida
```

<Callout title="Nodo no es un diccionario plano" type="info">
  Los datos pueden organizarse por rutas jerárquicas como `Perfil/Nivel` o `Estado/Vida`. Esto permite representar estructuras complejas y serializarlas como una única entidad.
</Callout>

## Crear un nodo [#crear-un-nodo]

```csharp
var datos = new Nodo("Perfil");
datos.SetChild("Nivel", 25);
datos.SetChild("Clase", "Caballero");
```

También puedes asignar directamente un valor:

```csharp
var nivel = new Nodo("Nivel", 25);
int valor = nivel.Get<int>();
```

## Rutas jerárquicas [#rutas-jerárquicas]

Eco permite leer y escribir usando rutas separadas por `/`:

```csharp
datos.SetHierarchy("Perfil/Nivel", 25);
datos.SetHierarchy("Perfil/Clase", "Caballero");

int nivel = datos.GetHierarchy<int>("Perfil/Nivel", 1);
```

Esto hace que `Nodo` sea especialmente útil para datos de servidor, canal, jugador y objeto.

## Añadir y reemplazar hijos [#añadir-y-reemplazar-hijos]

```csharp
datos.AddChild("Nivel", 25);
datos.AddMissingChild("Clase", "Caballero");
datos.ReplaceChild(new Nodo("Nivel", 30));
datos.UpdateChild("Nivel", 31, true);
```

La diferencia importante es si quieres añadir siempre, añadir sólo cuando falta o reemplazar el valor existente.

## Lectura tipada [#lectura-tipada]

```csharp
int nivel = datos.GetHierarchy<int>("Perfil/Nivel", 1);
float velocidad = datos.GetHierarchy<float>("Estadisticas/Velocidad", 5f);
```

`Nodo` utiliza `Serializacion.Convert` cuando el valor almacenado no coincide exactamente con el tipo solicitado.

## Caché de búsquedas [#caché-de-búsquedas]

Las búsquedas de hijos pueden utilizar una caché interna cuando un nodo contiene suficientes hijos. Si manipulas manualmente la colección `children`, debes invalidarla:

```csharp
datos.MarkChildrenAsChanged();
```

Normalmente no necesitas hacerlo cuando utilizas `AddChild`, `SetChild` o los métodos de alto nivel.

## Serialización [#serialización]

`Nodo` puede representarse en texto, binario o comprimido. La capacidad está integrada con las herramientas de serialización de Eco y se utiliza para persistencia y transmisión de estructuras de datos.

```text
Nodo
 ↓
Serializacion
 ├── Texto
 ├── Binario
 └── Comprimido
```

## Implementar serialización propia [#implementar-serialización-propia]

Una clase puede implementar `IDataNodeSerializable` para controlar cómo se representa en `Nodo`:

```csharp
public class DatosUnidad : IDataNodeSerializable
{
    public int nivel;
    public string clase;

    public void Serialize(Nodo node)
    {
        node.SetChild("Nivel", nivel);
        node.SetChild("Clase", clase);
    }

    public void Deserialize(Nodo node)
    {
        nivel = node.GetHierarchy<int>("Nivel", 1);
        clase = node.GetHierarchy<string>("Clase", "Desconocida");
    }
}
```

## Cuándo utilizarlo [#cuándo-utilizarlo]

`Nodo` es apropiado para:

* datos persistentes;
* configuración de canales;
* datos del jugador;
* estado de objetos;
* serialización de estructuras complejas;
* almacenamiento que deba sobrevivir a cambios de versión.

Para una acción puntual no necesitas construir manualmente un `Nodo` si los parámetros de la RFC ya son serializables.

## Errores habituales [#errores-habituales]

<Callout title="No abuses de rutas de texto" type="warn">
  Las rutas son cómodas, pero en código de alta frecuencia conviene evitar reconstruir árboles innecesariamente. Mantén referencias a nodos cuando una estructura se consulta continuamente.
</Callout>

## Código fuente [#código-fuente]

<Card title="Nodo.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Comun/Nodo.cs">
  Implementación actual de la estructura jerárquica.
</Card>

<Card title="Serialización" href="/docs/red/v1/referencia/api/serializacion">
  Conversión entre tipos de C# y representaciones de Eco.
</Card>
