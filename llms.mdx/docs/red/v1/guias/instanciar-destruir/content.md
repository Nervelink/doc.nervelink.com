# Instanciar y destruir objetos (/docs/red/v1/guias/instanciar-destruir)



# Instanciar y destruir objetos [#instanciar-y-destruir-objetos]

Los objetos dinámicos aparecen durante la ejecución y deben ser conocidos por el servidor y por los clientes que participan en su canal. Eco proporciona métodos de conveniencia desde `Componente` y `Eco` para solicitar su creación y destrucción.

<Callout title="Idea principal" type="info">
  Instanciar un `GameObject` de Unity no lo convierte automáticamente en un objeto de red. La creación debe pasar por el flujo de Eco para que el objeto reciba identidad, canal y propietario de red.
</Callout>

## Objeto estático frente a dinámico [#objeto-estático-frente-a-dinámico]

Eco distingue entre objetos presentes en la escena y objetos creados durante la partida:

| Tipo     | Identidad                        | Ciclo de vida                        |
| -------- | -------------------------------- | ------------------------------------ |
| Estático | ID persistente de escena         | Existe al cargar el nivel            |
| Dinámico | ID asignado durante la ejecución | Se crea y destruye durante la sesión |

Los IDs estáticos están en el rango `1`–`32767`. Los dinámicos comienzan en `32768`. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs)

## Requisito: prefab registrable [#requisito-prefab-registrable]

Para instanciar un objeto en red, Eco necesita localizar el recurso que se va a crear. El path utilizado por `Instanciar` debe corresponder al recurso esperado por la infraestructura de Eco.

El objeto resultante debe incluir un `Objeto` y los `Componente` que implementen su comportamiento de red.

```text
Prefab
├── Objeto
├── Componente
└── Componente...
```

`Componente` puede recuperar automáticamente su `Objeto` asociado durante su ciclo de inicialización. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Componente.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Componente.cs)

## Instanciar desde un componente [#instanciar-desde-un-componente]

`Componente` proporciona dos sobrecargas de `Instantiate` que delegan en `Eco.Instanciar` utilizando el canal del objeto asociado:

```csharp
Instantiate(rccID, "Unidades/Guerrero", false, datos...);
```

O utilizando el nombre de una función de creación:

```csharp
Instantiate("CrearGuerrero", "Unidades/Guerrero", false, datos...);
```

El flujo es:

```text
Componente
   ↓
Instanciar(...)
   ↓
Eco.Instanciar(...)
   ↓
solicitud de creación
   ↓
servidor / canal
   ↓
objeto dinámico
```

La sobrecarga con `rccID` utiliza un identificador numérico de creación; la otra utiliza un nombre de función. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Componente.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Componente.cs)

## Canal de creación [#canal-de-creación]

La creación se realiza en el canal del objeto desde el que se solicita:

```csharp
Instantiate("CrearGuerrero", "Unidades/Guerrero", false);
```

Conceptualmente:

```text
Objeto origen
    │
    └── channelID = 20
            │
            ▼
       Eco.Instanciar
            │
            ▼
        Canal 20
            │
            ▼
       nuevo Objeto
```

Esto evita crear accidentalmente una entidad en un canal diferente del contexto de gameplay que la originó.

## Persistencia [#persistencia]

La llamada de creación recibe un parámetro `persistent`:

```csharp
Instantiate("CrearGuerrero", "Unidades/Guerrero", true);
```

La persistencia indica que el objeto forma parte del estado que el servidor debe conservar para futuras restauraciones o participantes, frente a un objeto puramente temporal de la sesión.

La elección debe hacerse por semántica del objeto:

```text
¿Debe existir al restaurar el estado del canal?
        │
    ┌───┴───┐
   sí       no
   │         │
true      false
```

No utilices `persistent = true` simplemente porque el objeto deba ser visible para todos; son conceptos diferentes.

## Ownership [#ownership]

Un objeto dinámico puede tener propietario. `Objeto` expone:

```csharp
objeto.owner
objeto.ownerID
objeto.isMine
```

Los objetos dinámicos permiten transferir la propiedad a un jugador válido del canal o retirarla mediante `ownerID = 0`. Los objetos estáticos no permiten cambiar su propietario de esta forma. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs)

```text
Objeto dinámico
      │
      ├── propietario = Jugador A
      │
      └── puede transferirse
```

La propiedad importa porque determinados cambios de estado y operaciones de red dependen de quién tiene autoridad.

## Saber cuándo ha terminado la creación [#saber-cuándo-ha-terminado-la-creación]

Un `Objeto` distingue entre estar creado localmente y haber sido registrado por completo:

```csharp
objeto.hasBeenRegistered
```

La propiedad pasa a `true` cuando finaliza la creación y el objeto recibe su ID de red. Antes de eso, su identidad todavía no está preparada para todas las operaciones de red. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs)

Por tanto, un patrón como este es más seguro:

```csharp
if (objeto.hasBeenRegistered && objeto.PuedeEnviar)
{
    // Operación de red.
}
```

## Destruir un objeto [#destruir-un-objeto]

Desde un `Componente` puede utilizarse:

```csharp
DestroySelf();
```

La llamada delega en el `Objeto` asociado y solicita la destrucción de la entidad de red. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Componente.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Componente.cs)

También existe una sobrecarga con retraso y control sobre quién puede solicitarla:

```csharp
DestroySelf(2f, true);
```

El segundo parámetro permite exigir que la operación sólo se ejecute cuando corresponda al propietario.

## Destrucción local frente a destrucción de red [#destrucción-local-frente-a-destrucción-de-red]

No es equivalente hacer:

```csharp
Destroy(gameObject);
```

a hacer:

```csharp
DestroySelf();
```

La primera operación sólo actúa directamente sobre el `GameObject` local. La segunda utiliza la capa de red y permite que la destrucción se propague al resto de participantes y al servidor según el estado de la entidad.

```text
Destroy(gameObject)
      ↓
solo Unity local

DestroySelf()
      ↓
Objeto de red
      ↓
servidor / canal
      ↓
otros clientes
```

## Estado de destrucción [#estado-de-destrucción]

Cuando se solicita la destrucción, Eco marca el objeto inmediatamente como destruido. Esto evita que se sigan enviando RFC entre la solicitud local y la confirmación del servidor. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs)

Puede comprobarse mediante:

```csharp
objeto.hasBeenDestroyed
```

Por eso el estado útil de un objeto dinámico puede visualizarse como:

```text
Creación
   ↓
Registro pendiente
   ↓
Registrado
   ↓
Activo
   ↓
Destrucción solicitada
   ↓
Destruido
```

## Callbacks de ciclo de vida [#callbacks-de-ciclo-de-vida]

`Objeto` expone `onDestroy` para ejecutar lógica antes de las notificaciones normales de `OnDestroy` de Unity. También expone `onTransfer` cuando el objeto cambia de canal. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs)

Esto permite separar eventos de red de los callbacks generales de Unity.

## Evitar instanciación local directa [#evitar-instanciación-local-directa]

No es recomendable crear un prefab de red mediante:

```csharp
Instantiate(prefab);
```

cuando el objeto deba existir para los demás participantes. Esto sólo crea una instancia local y no establece por sí mismo una identidad de red.

El patrón correcto es pasar por el sistema de instanciación de Eco.

## Ejemplo completo [#ejemplo-completo]

Un componente puede crear una unidad y eliminarla posteriormente:

```csharp
public class GestorUnidades : Componente
{
    public void CrearUnidad()
    {
        Instantiate("CrearUnidad", "Unidades/Guerrero", false, "caballero");
    }

    public void DestruirUnidad()
    {
        DestroySelf();
    }
}
```

La entidad creada obtiene su propio `Objeto`, canal, ID dinámico y propietario según las reglas del servidor.

## Errores habituales [#errores-habituales]

<Callout title="El prefab existe localmente pero no aparece en otros clientes" type="warn">
  Comprueba que estás utilizando `Eco.Instanciar` o `Componente.Instantiate` y que el recurso está disponible para la infraestructura de creación. Un `Instantiate` de Unity por sí solo no registra el objeto en la red.
</Callout>

<Callout title="Intentar enviar inmediatamente después de crear" type="warn">
  Comprueba `hasBeenRegistered` y `PuedeEnviar`. Durante el registro inicial el objeto puede existir en Unity pero todavía no tener una identidad de red utilizable.
</Callout>

<Callout title="Destruir con Destroy()" type="warn">
  Si el objeto es una entidad de red, utiliza `DestroySelf()` para que la destrucción siga el ciclo de Eco.
</Callout>

## Referencias [#referencias]

<Card title="Objeto" href="/docs/red/v1/modelo/objetos">
  Identidad, ownership y ciclo de vida del objeto de red.
</Card>

<Card title="Componente" href="/docs/red/v1/modelo/componentes">
  Comportamiento asociado al objeto y métodos de conveniencia.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Implementación upstream de referencia histórica.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Referencia externa para contrastar la arquitectura heredada.
</Card>
