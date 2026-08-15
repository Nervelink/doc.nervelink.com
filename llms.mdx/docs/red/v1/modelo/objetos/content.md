# Objetos (/docs/red/v1/modelo/objetos)



# Objetos [#objetos]

Un `Objeto` es la identidad de red asociada a un `GameObject` de Unity. Permite a Eco localizar una entidad, conocer su canal, determinar su propietario y asociar los `Componente` que implementan su comportamiento de red.

<Callout title="Idea principal" type="info">
  Un objeto no es simplemente un contenedor para RPC. Es la identidad de red sobre la que Eco construye direccionamiento, propiedad, estado y ciclo de vida.
</Callout>

## Identidad [#identidad]

Cada objeto tiene un `id` y un `uid`.

| Tipo     | Rango              | Uso                                 |
| -------- | ------------------ | ----------------------------------- |
| Estático | `1`–`32767`        | Objetos que ya existen en la escena |
| Dinámico | `32768`–`16777215` | Objetos creados durante la sesión   |

El `uid` combina canal e identificador del objeto:

```text
UID = Canal << 32 | ID
```

Los objetos estáticos y dinámicos tienen ciclos de vida diferentes. Por ejemplo, los objetos estáticos no permiten cambiar su propietario de la misma forma que los dinámicos.

## Canal y registro [#canal-y-registro]

Cada objeto tiene asociado un `channelID`. `hasBeenRegistered` indica que su creación ya terminó y que tiene un identificador de red asignado. `hasBeenDestroyed` bloquea el uso del objeto cuando comienza su destrucción.

```text
Creación
   ↓
Registro
   ↓
Activo
   ↓
Destrucción solicitada
   ↓
Destruido
```

## Propiedad [#propiedad]

Las propiedades principales son:

```csharp
objeto.isMine
objeto.owner
objeto.ownerID
```

`isMine` comprueba la propiedad local, `owner` devuelve el `Jugador` propietario y `ownerID` expone su identificador.

```text
Objeto
   │
   └── ownerID → Jugador
```

Ser visible para un jugador no implica que sea suyo. La propiedad representa autoridad para determinadas operaciones de red.

## Componentes [#componentes]

Los `Componente` contienen el comportamiento de red asociado al `Objeto`.

```text
GameObject
├── Objeto
├── Componente A
├── Componente B
└── Componente C
```

Un `Componente` busca el `Objeto` en su jerarquía de Unity y expone accesos de conveniencia para datos, instanciación y destrucción.

## Datos [#datos]

Cada objeto puede mantener un `Nodo` de datos:

```csharp
objeto.Set("vida", 100);
int vida = objeto.Get<int>("vida");
```

El cambio se aplica localmente y, cuando la sincronización está activa, Eco decide cómo propagarlo según propiedad y disponibilidad del objeto para enviar. Los cambios pueden quedar en cola mientras el objeto no puede transmitir.

## Capacidad de envío [#capacidad-de-envío]

`PuedeEnviar` comprueba que el objeto tenga un identificador válido, no esté destruido, pertenezca a un canal del cliente y no esté todavía completando la entrada en ese canal.

Esto resulta especialmente importante durante cambios de escena y uniones a canales.

## Ciclo de vida y transferencia [#ciclo-de-vida-y-transferencia]

`Objeto` expone callbacks de red para destrucción y transferencia:

```text
onDestroy
onTransfer
```

Estos callbacks permiten separar el ciclo de vida de red del ciclo de vida de Unity.

## Relación con Eco [#relación-con-eco]

```text
                Eco
                 │
       ┌─────────┼─────────┐
       │         │         │
   Conexión    Canales   Jugadores
                 │
                 ▼
               Objeto
                 │
            ┌────┴────┐
            │         │
       Propiedad    Datos
            │
       Componentes
```

## Correspondencia con TNet [#correspondencia-con-tnet]

| Eco          | TNet          | Función                         |
| ------------ | ------------- | ------------------------------- |
| `Objeto`     | `TNObject`    | Identidad y estado de red       |
| `Componente` | `TNBehaviour` | Comportamiento asociado         |
| `owner`      | `owner`       | Jugador propietario             |
| `ownerID`    | `ownerID`     | Identificador del propietario   |
| `isMine`     | `isMine`      | Comprueba propiedad local       |
| `uid`        | `uid`         | Canal + identificador de objeto |

## Referencias [#referencias]

<Card title="Objeto.cs · Eco" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs">
  Implementación actual de `Objeto`.
</Card>

<Card title="Componente.cs · Eco" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Componente.cs">
  Implementación de los componentes asociados a un objeto.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Referencia generada sobre la implementación actual de TNet.
</Card>
