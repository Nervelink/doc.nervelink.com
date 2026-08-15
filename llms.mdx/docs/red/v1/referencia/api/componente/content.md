# Componente (/docs/red/v1/referencia/api/componente)



# Componente [#componente]

`Componente` es la clase base que simplifica la creación de comportamientos de Unity que necesitan comunicarse mediante Eco.

```csharp
public class MiUnidad : Componente
{
    // lógica de gameplay y red
}
```

El componente busca automáticamente un `Objeto` en su jerarquía y mantiene una referencia a él.

## Relación con Unity [#relación-con-unity]

```text
GameObject
├── Objeto
└── MiUnidad : Componente
```

Si el `Componente` no encuentra un `Objeto`, Eco puede crear uno automáticamente, aunque esto no sustituye a configurar correctamente el prefab para una instancia de red.

<Callout title="Prefabs de red" type="warn">
  Para un prefab que vaya a utilizar comunicación de red, añade explícitamente `Objeto` a la jerarquía del prefab. La creación automática es una red de seguridad, no una configuración recomendada de producción.
</Callout>

## Acceso al objeto [#acceso-al-objeto]

La referencia se obtiene mediante la propiedad `ero`.

```csharp
Objeto objeto = ero;

if (ero.isMine)
{
    ero.Set("vida", 100);
}
```

## Datos [#datos]

`Componente` expone accesos de conveniencia a `Get` y `Set`:

```csharp
int vida = Get<int>("vida", 100);
Set("vida", vida - 10);
```

También admite la sintaxis textual:

```csharp
Set("vida = 100");
```

Esta sintaxis utiliza `Nodo` para interpretar el valor.

## Sincronización inmediata [#sincronización-inmediata]

`immediateSync` determina si las llamadas a `Set` deben pedir sincronización inmediatamente por defecto.

```csharp
immediateSync = false;
Set("contador", contador, sync: true);
```

El parámetro `sync` y `immediateSync` permiten separar modificación local de sincronización.

## Instanciación [#instanciación]

Desde un `Componente` puedes instanciar un objeto en el mismo canal del objeto asociado:

```csharp
Instantiate("CreateAtPosition", "Units/Soldier", true, transform.position, transform.rotation);
```

También puedes utilizar un ID de RCR.

## Destrucción [#destrucción]

```csharp
DestroySelf();
DestroySelf(2f);
```

La segunda variante puede limitar la destrucción a determinados propietarios.

## RMR guardadas [#rmr-guardadas]

`RemoveAllSavedRFCs()` elimina las RFC guardadas asociadas a los métodos marcados para persistencia en el componente.

Es útil cuando un objeto persistente necesita reconstruir su estado desde cero.

## Ciclo de inicialización [#ciclo-de-inicialización]

```text
Awake
  ↓
buscar Objeto
  ↓
ActualizadorRed
  ↓
OnStart
  ↓
Objeto registrado
```

`Componente` utiliza `ActualizadorRed` para diferir parte de la inicialización hasta el momento adecuado del runtime.

## Código fuente [#código-fuente]

<Card title="Componente.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Componente.cs">
  Implementación actual de `Componente`.
</Card>

<Card title="Objeto" href="/docs/red/v1/referencia/api/objeto">
  Identidad de red que respalda al componente.
</Card>
