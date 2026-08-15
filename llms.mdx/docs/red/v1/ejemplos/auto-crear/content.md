# AutoCrear (/docs/red/v1/ejemplos/auto-crear)



# AutoCrear [#autocrear]

`AutoCrear` es un helper que espera a que el cliente termine de entrar en un canal y crea después un prefab de red en la posición del `GameObject` que contiene el componente.

## Configuración [#configuración]

```csharp
channelID = 0;
prefabPath = "Player/Avatar";
persistent = false;
```

Cuando `channelID` es menor que `1`, utiliza el último canal registrado por Eco. En proyectos multicanal es preferible establecer el canal explícitamente.

## Flujo [#flujo]

<Steps>
  <Step>
    ### Resolver canal [#resolver-canal]

    Si no se ha indicado un canal, toma el último canal registrado por Eco.
  </Step>

  <Step>
    ### Esperar disponibilidad [#esperar-disponibilidad]

    Espera mientras Eco se está uniendo a canales o mientras el canal indicado todavía no está activo.
  </Step>

  <Step>
    ### Crear [#crear]

    Llama a `Eco.Instanciar(...)` con la posición y rotación del objeto.
  </Step>

  <Step>
    ### Eliminar helper [#eliminar-helper]

    El `AutoCrear` original se destruye porque su trabajo ha terminado.
  </Step>
</Steps>

## Persistencia [#persistencia]

`persistent = false` es apropiado para avatares o entidades ligadas al jugador. Usa `true` cuando la entidad deba permanecer en el canal aunque abandone su creador.

## RCR local [#rcr-local]

El ejemplo registra una RCR mediante `[RCR]` para crear el prefab en la posición y rotación recibidas.

```csharp
[RCR]
static GameObject CreateAtPosition(GameObject prefab, Vector3 pos, Quaternion rot)
{
    return prefab.Instantiate(pos, rot);
}
```

Esto ilustra el patrón de creación remota de Eco: la operación se describe con datos serializables y cada cliente reconstruye la entidad localmente.

## Fuente [#fuente]

<Card title="AutoCrear.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/AutoCrear.cs">
  Implementación actual del ejemplo.
</Card>

<Card title="Instanciar y destruir" href="/docs/red/v1/guias/instanciar-destruir">
  Workflow completo de ciclo de vida de objetos.
</Card>
