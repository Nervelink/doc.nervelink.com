# Objeto (/docs/red/v1/referencia/api/objeto)



# Objeto [#objeto]

`Objeto` representa una entidad de red concreta. Vincula la identidad del objeto de Unity con un canal, un propietario, datos sincronizados y el despacho de llamadas remotas.

## Identidad [#identidad]

```csharp
uint id = objeto.id;
ulong uid = objeto.uid;
int canal = objeto.channelID;
```

`id` pertenece al contexto del canal. `uid` combina la identidad necesaria para resolver el objeto entre contextos de red.

| Miembro             | Uso                                         |
| ------------------- | ------------------------------------------- |
| `id`                | Identificador del objeto dentro del canal   |
| `uid`               | Identificador global de red                 |
| `channelID`         | Canal actual                                |
| `hasBeenRegistered` | Registro completado                         |
| `hasBeenDestroyed`  | Destrucción procesada                       |
| `isMine`            | El propietario es el jugador local          |
| `ownerID`           | ID del propietario                          |
| `owner`             | `Jugador` propietario                       |
| `PuedeEnviar`       | El objeto puede realizar operaciones de red |

## Estado y datos [#estado-y-datos]

```csharp
objeto.Set("vida", 100);
int vida = objeto.Get<int>("vida", 100);
Nodo nodo = objeto.Get("vida");
```

El patrón es clave/valor sobre `Nodo`. `Set` actualiza localmente y genera la sincronización cuando la autoridad y el estado de conexión permiten transmitirla.

## Autoridad [#autoridad]

```csharp
if (objeto.isMine)
{
    objeto.Set("vida", nuevaVida);
}
```

Cuando el emisor no es propietario, el cambio puede requerir una solicitud al propietario. No utilices `isMine` como sistema de seguridad: las reglas críticas deben validarse en el lado con autoridad.

## PuedeEnviar [#puedeenviar]

```csharp
if (objeto.PuedeEnviar)
{
    objeto.Send("Actualizar", Objetivo.Otros, valor);
}
```

Esta propiedad evita enviar cuando el objeto todavía no está registrado, está destruido, no pertenece a un canal válido o atraviesa una transición en la que el protocolo no admite todavía la operación.

## RFC [#rfc]

Los métodos remotos se declaran en `Componente` y se ejecutan dentro del contexto del `Objeto`:

```csharp
public class Unidad : Componente
{
    [RMR]
    void RecibirDaño(int cantidad)
    {
        // validar y aplicar
    }
}
```

El objeto proporciona identidad y destinatario; el componente proporciona el método remoto.

## Instanciación y destrucción [#instanciación-y-destrucción]

Desde la fachada:

```csharp
Eco.Instanciar(canal, rcrID, nombreRcr, prefab, persistente, parametros);
```

Desde un componente:

```csharp
Instantiate("CrearUnidad", "Units/Soldier", false, posicion);
DestroySelf();
```

Las operaciones de destrucción deben respetar la autoridad correspondiente.

## Ownership [#ownership]

El propietario puede cambiarse únicamente bajo las reglas de Eco para objetos dinámicos y jugadores del mismo canal. No edites directamente `ownerID` para simular una transferencia sin ejecutar el flujo de ownership correspondiente.

## Transferencia de canal [#transferencia-de-canal]

La transferencia entre canales cambia el contexto del objeto y puede producir un nuevo `id`. `onTransfer` permite actualizar referencias locales:

```csharp
objeto.onTransfer += (nuevoCanal, nuevoID) =>
{
    // Invalidar referencias basadas en el ID anterior.
};
```

## Persistencia [#persistencia]

Un objeto persistente y un objeto estático de escena no son equivalentes. Los objetos persistentes del servidor se reconstruyen a partir del estado guardado del canal y de las operaciones necesarias para restaurarlos.

## Eventos [#eventos]

Entre los eventos relevantes están la destrucción y transferencia. Suscríbelos para sincronizar sistemas locales que dependen del ciclo de vida del objeto.

## Búsqueda [#búsqueda]

Cuando el código debe resolver una entidad recibida por protocolo, utiliza el identificador y el canal con los mecanismos de búsqueda de Eco en lugar de mantener referencias globales a `GameObject`.

<Callout title="No guardes GameObject como identidad de red" type="warn">
  Una referencia de Unity no sustituye a `id`, `uid` y `channelID`. Durante carga de escenas, creación, transferencia y destrucción, el `GameObject` puede cambiar mientras la identidad de red sigue un ciclo controlado por Eco.
</Callout>

## Equivalencia TNet [#equivalencia-tnet]

| Eco          | TNet                                    |
| ------------ | --------------------------------------- |
| `Objeto`     | `TNObject`                              |
| `Componente` | `TNBehaviour`                           |
| `uid`        | `fullID` / `uid` según versión upstream |
| `ownerID`    | `ownerID`                               |

<Card title="Objeto.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs">
  Implementación actual.
</Card>

<Card title="Objetos" href="/docs/red/v1/modelo/objetos">
  Modelo conceptual y ciclo de vida.
</Card>
