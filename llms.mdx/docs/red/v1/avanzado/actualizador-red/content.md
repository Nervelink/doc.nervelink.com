# ActualizadorRed (/docs/red/v1/avanzado/actualizador-red)



# ActualizadorRed [#actualizadorred]

`ActualizadorRed` es un scheduler centralizado utilizado por Eco para ejecutar `OnStart`, `OnUpdate`, `OnLateUpdate`, actualizaciones poco frecuentes, callbacks de una sola ejecución y pequeñas coroutines.

## Por qué existe [#por-qué-existe]

Unity ejecuta `Update` y `LateUpdate` de cada `MonoBehaviour` habilitado. Cuando un proyecto contiene cientos o miles de componentes pequeños, centralizar parte de esos ciclos puede reducir overhead y mejorar el control del scheduler.

```text
MonoBehaviour A ─┐
MonoBehaviour B ─┼──► ActualizadorRed ─► OnUpdate()
MonoBehaviour C ─┘
```

## Contratos [#contratos]

```csharp
public interface IStartable
{
    void OnStart();
}

public interface IUpdateable
{
    void OnUpdate();
}

public interface ILateUpdateable
{
    void OnLateUpdate();
}

public interface IInfrequentUpdateable
{
    void InfrequentUpdate();
}
```

## Alta y baja [#alta-y-baja]

```csharp
ActualizadorRed.AddUpdate(this);
ActualizadorRed.AddLateUpdate(this);
ActualizadorRed.AddInfrequentUpdate(this, 0.25f);

ActualizadorRed.RemoveUpdate(this);
ActualizadorRed.RemoveLateUpdate(this);
ActualizadorRed.RemoveInfrequentUpdate(this);
```

La implementación utiliza conjuntos para las actualizaciones normales y colas auxiliares cuando un objeto se elimina durante el propio recorrido.

## Una sola ejecución [#una-sola-ejecución]

```csharp
ActualizadorRed.AddOneShot(() =>
{
    Debug.Log("Ejecutado en el siguiente Update");
});
```

Esto es útil para diferir una operación al siguiente ciclo sin crear un `MonoBehaviour` adicional.

## Invoke sin MonoBehaviour [#invoke-sin-monobehaviour]

```csharp
ActualizadorRed.Invoke(() =>
{
    Debug.Log("Han pasado 2 segundos");
}, 2f);
```

## Coroutines [#coroutines]

El scheduler también puede mantener coroutines basadas en `TareaParalela.EnumFunc` y procesarlas fuera del `MonoBehaviour` que las originó.

## Thread safety [#thread-safety]

La implementación actual permite `THREAD_SAFE_UPDATER`, protegiendo las colecciones internas con lock. Esto es especialmente importante cuando una tarea de red o worker registra callbacks desde otro hilo.

<Callout title="No mezclar con Unity API desde cualquier hilo" type="warn">
  Que el scheduler sea seguro para registrar callbacks no significa que `UnityEngine` sea thread-safe. Los callbacks que toquen objetos de Unity deben ejecutarse en el hilo de Unity.
</Callout>

## Relación con Componente [#relación-con-componente]

`Componente.Awake()` registra su inicialización mediante `ActualizadorRed.AddStart(this)`. Por eso la documentación de `Componente` no debe asumir que toda la inicialización de red ocurre directamente en `Start()`.

<Card title="Componente" href="/docs/red/v1/referencia/api/componente">
  Clase base que utiliza `ActualizadorRed` durante la inicialización.
</Card>

<Card title="ActualizadorRed.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/ActualizadorRed.cs">
  Implementación actual del scheduler.
</Card>
