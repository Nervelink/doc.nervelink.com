# Componente (/docs/red/v1/referencia/api/componente)



# Componente [#componente]

`Componente` es la clase base para comportamientos de Unity que necesitan identidad de red, estado sincronizado, RFC, instanciación o destrucción.

## Herencia [#herencia]

```csharp
public class MiUnidad : Componente
{
}
```

El componente busca un `Objeto` en su jerarquía durante `Awake()` y prepara su tabla de métodos remotos.

## API principal [#api-principal]

| Miembro                    | Propósito                                              |
| -------------------------- | ------------------------------------------------------ |
| `ero`                      | `Objeto` asociado, creado/resuelto cuando es necesario |
| `Get<T>(nombre, defecto)`  | Leer un dato                                           |
| `Set(nombre, valor, sync)` | Escribir un dato                                       |
| `immediateSync`            | Sincronización por defecto de `Set`                    |
| `Instantiate(...)`         | Crear un objeto en el canal del componente             |
| `DestroySelf()`            | Destruir la entidad de red                             |
| `RemoveAllSavedRFCs()`     | Eliminar RFC guardadas asociadas                       |

## Obtener datos [#obtener-datos]

```csharp
int vida = Get<int>("vida", 100);
Nodo nodo = Get("vida");
```

La sobrecarga con valor por defecto evita depender de la existencia previa del nodo.

## Establecer datos [#establecer-datos]

```csharp
Set("vida", 100);
Set("vida", 100, sync: false);
immediateSync = false;
```

También acepta una expresión textual de la forma `clave = valor`:

```csharp
Set("vida = 100");
```

La conversión textual depende del sistema `Nodo` y no es la opción recomendada para datos de alto rendimiento.

## Instanciación [#instanciación]

La instancia se crea en el canal del `Objeto` asociado:

```csharp
Instantiate(
    "CrearUnidad",
    "Units/Soldier",
    persistent: false,
    transform.position
);
```

Existe además la variante que recibe el ID de una RCR.

## Destrucción [#destrucción]

```csharp
DestroySelf();
DestroySelf(2f);
```

La variante con retraso permite programar la destrucción manteniendo las reglas de ownership establecidas por Eco.

## Inicio diferido [#inicio-diferido]

`Componente` utiliza `ActualizadorRed` para diferir `OnStart()` y evitar depender del `Start()` estándar de Unity para la preparación del objeto de red.

```text
Awake()
  ↓
Objeto asociado
  ↓
ActualizadorRed
  ↓
OnStart()
  ↓
Métodos remotos preparados
```

Esto es especialmente importante en jerarquías y prefabs que se registran durante cambios de escena.

## Prefabs [#prefabs]

La configuración recomendada es colocar explícitamente `Objeto` en el prefab que represente una entidad de red. La creación automática de `Objeto` es una red de seguridad y no una sustitución del setup del prefab.

## RFC guardadas [#rfc-guardadas]

`RemoveAllSavedRFCs()` inspecciona métodos marcados como RFC persistentes y elimina sus entradas guardadas para el objeto.

## Código fuente [#código-fuente]

<Card title="Componente.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Componente.cs">
  Implementación actual.
</Card>

<Card title="Objeto" href="/docs/red/v1/referencia/api/objeto">
  Identidad de red asociada al componente.
</Card>

<Card title="ActualizadorRed" href="/docs/red/v1/avanzado/actualizador-red">
  Scheduler que controla el ciclo de inicio y actualización.
</Card>
