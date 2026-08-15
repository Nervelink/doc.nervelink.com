# Objeto (/docs/red/v1/referencia/api/objeto)



# Objeto [#objeto]

`Objeto` es la identidad de red asociada a una entidad de Unity. Eco lo utiliza para localizar componentes, mantener ownership, asociar datos y enrutar RFC.

## Identidad [#identidad]

```csharp
uint id = objeto.id;
ulong uid = objeto.uid;
int canal = objeto.channelID;
```

El objeto mantiene además información de registro y destrucción:

```csharp
objeto.hasBeenRegistered;
objeto.hasBeenDestroyed;
```

## Ownership [#ownership]

```csharp
bool local = objeto.isMine;
Jugador propietario = objeto.owner;
int idPropietario = objeto.ownerID;
```

El cambio de propietario debe hacerse sobre objetos dinámicos y el nuevo propietario debe pertenecer al canal correspondiente.

```csharp
if (objeto.id >= 32768)
{
    objeto.ownerID = nuevoJugador.id;
}
```

## Estado [#estado]

`Objeto` contiene un `Nodo` de datos específico:

```csharp
objeto.Set("vida", 100);
int vida = objeto.Get<int>("vida", 100);
```

`Set` actualiza el dato localmente y, cuando procede, genera la comunicación necesaria. Las solicitudes de un cliente que no es propietario se encaminan al propietario.

## Capacidad de envío [#capacidad-de-envío]

```csharp
if (objeto.PuedeEnviar)
{
    objeto.Send("Actualizar", Objetivo.Otros, datos);
}
```

`PuedeEnviar` considera conectividad, ID válido, destrucción, pertenencia al canal y unión pendiente.

<Callout title="Comprueba PuedeEnviar en transiciones" type="warn">
  No asumas que un objeto creado localmente puede comunicarse inmediatamente. Las entradas de canal y cargas de nivel generan estados transitorios.
</Callout>

## RFC [#rfc]

Los componentes asociados al objeto pueden declarar métodos remotos y el objeto proporciona el contexto necesario para enviarlos.

```csharp
public class Unidad : Componente
{
    [RMR]
    void RecibirDaño(int cantidad)
    {
        // ...
    }
}
```

## Persistencia [#persistencia]

El servidor puede almacenar RFC y datos de objetos dinámicos según la forma de creación y el objetivo del mensaje. Un objeto dinámico persistente puede sobrevivir a la salida de su propietario.

## Transferencia [#transferencia]

Los objetos dinámicos pueden trasladarse a otro canal. Durante la operación el ID puede cambiar; `onTransfer` permite reaccionar localmente.

```csharp
objeto.onTransfer += (nuevoCanal, nuevoID) =>
{
    // actualizar referencias locales
};
```

## Destrucción [#destrucción]

```csharp
objeto.onDestroy += () =>
{
    // limpieza previa
};

objeto.DestroySelf();
```

## Búsqueda [#búsqueda]

La implementación permite localizar objetos por canal e ID y resolver identificadores globales para operaciones internas de Eco.

## Referencia con TNet [#referencia-con-tnet]

| Eco          | TNet          |
| ------------ | ------------- |
| `Objeto`     | `TNObject`    |
| `Componente` | `TNBehaviour` |
| `isMine`     | `isMine`      |
| `ownerID`    | `ownerID`     |

<Card title="Objeto.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs">
  Fuente de la API actual.
</Card>

<Card title="Objetos" href="/docs/red/v1/modelo/objetos">
  Explicación conceptual del modelo de objetos.
</Card>
