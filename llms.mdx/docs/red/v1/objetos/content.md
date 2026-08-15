# Objetos (/docs/red/v1/objetos)



# Objetos [#objetos]

Un `Objeto` es la identidad de red asociada a un `GameObject` de Unity. Es el punto de referencia que permite a Eco localizar una entidad, conocer su canal, determinar su propietario y asociar los `Componente` que implementan su comportamiento de red.

<Callout title="Idea principal" type="info">
  Un objeto no es simplemente un contenedor para RPC. Es la identidad de red sobre la que Eco construye direccionamiento, propiedad, estado y ciclo de vida.
</Callout>

## Estructura [#estructura]

Conceptualmente, un objeto reúne:

```text
Objeto
├── ID de red
├── Canal
├── Propietario
├── Estado / datos
└── Componentes
```

En `eco`, `Objeto` deriva de `MonoBehaviour` y mantiene estructuras estáticas para registrar y localizar objetos por canal e identificador. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs)

## Identidad de red [#identidad-de-red]

Cada objeto tiene un `id` que Eco utiliza para dirigir mensajes al destino correcto.

Los identificadores se dividen en dos rangos:

| Tipo     | Rango              | Uso                                 |
| -------- | ------------------ | ----------------------------------- |
| Estático | `1`–`32767`        | Objetos que ya existen en la escena |
| Dinámico | `32768`–`16777215` | Objetos creados durante la sesión   |

El objeto también expone `uid`, que combina el identificador del canal con el identificador del objeto:

```text
UID = Canal << 32 | ID
```

Esto permite distinguir el mismo identificador de objeto en diferentes canales. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs)

<Callout title="Objetos estáticos y dinámicos" type="warn">
  Los objetos estáticos y dinámicos no tienen exactamente el mismo ciclo de vida. Entre otras diferencias, los objetos estáticos no permiten cambiar su propietario mediante `ownerID`, mientras que los dinámicos sí.
</Callout>

## Canal [#canal]

Cada objeto tiene asociado un `channelID`. La asociación al canal define el contexto de red en el que ese objeto puede ser localizado y en el que se evalúan operaciones como propiedad, envío y transferencia.

```text
Canal 12
├── Objeto 101
├── Objeto 102
└── Objeto 103
```

El código de Eco mantiene tablas de objetos indexadas por canal para permitir tanto iteración como búsqueda rápida. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs)

## Registro [#registro]

El objeto distingue entre haber sido registrado y estar todavía en proceso de creación.

`hasBeenRegistered` indica que la creación ha terminado y que el objeto ya tiene asignado su identificador de red correspondiente. `hasBeenDestroyed` marca el objeto inmediatamente cuando comienza su destrucción para impedir que se sigan enviando mensajes durante el intervalo entre la solicitud y la confirmación del servidor. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs)

El estado útil para razonar sobre un objeto es, por tanto:

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

La creación y destrucción concretas, incluida la instanciación dinámica, se documentarán en su propio apartado cuando entremos en el ciclo de vida completo.

## Capacidad de envío [#capacidad-de-envío]

`PuedeEnviar` indica si el objeto puede enviar mensajes en el estado actual.

En una aplicación desconectada, el objeto no queda bloqueado por la capa de red. Una vez conectada la aplicación, el envío requiere que:

* el objeto tenga un identificador válido;
* no esté destruido;
* el cliente pertenezca al canal del objeto;
* el cliente no esté todavía completando la entrada en ese canal.

Esto es especialmente relevante durante cambios de escena o uniones a canales, donde un objeto puede existir localmente antes de que la conexión esté preparada para enviar sus mensajes. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs)

## Propiedad [#propiedad]

La propiedad determina qué `Jugador` controla un objeto dinámico para las operaciones que requieren autoridad.

Las propiedades principales son:

```csharp
objeto.isMine
objeto.owner
objeto.ownerID
```

`isMine` permite comprobar si el propietario es el jugador local. `owner` devuelve el objeto `Jugador` asociado y `ownerID` expone su identificador.

```text
Objeto
   │
   └── ownerID → Jugador
                    │
                    └── id
```

Cuando se cambia `ownerID`, Eco actualiza primero el estado local y después envía `RequestSetOwner` al servidor. El nuevo propietario debe ser el jugador local, un jugador válido del canal o `0`. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs)

<Callout title="Autoridad" type="info">
  Ser visible para un jugador no significa ser suyo. Un objeto puede existir en varios clientes mientras uno de ellos mantiene la propiedad utilizada para determinadas operaciones de red.
</Callout>

## Componentes [#componentes]

Los `Componente` se asocian a un `Objeto` y contienen la lógica de red especializada.

Un `Componente` busca automáticamente un `Objeto` en su jerarquía de Unity durante `Awake`/`OnStart`. Si no encuentra uno y `ignoreMissingTNO` está desactivado, Eco puede crear un `Objeto` local para que el componente funcione, aunque el código advierte que esto por sí solo no garantiza una comunicación de red válida. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Componente.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Componente.cs)

```text
GameObject
├── Objeto
├── Componente A
├── Componente B
└── Componente C
```

Esta relación es importante: `Objeto` proporciona la identidad de red y los `Componente` proporcionan el comportamiento.

## Datos asociados [#datos-asociados]

Cada objeto puede mantener un `Nodo` de datos accesible mediante `Get`, `Get<T>`, `Set` y `nodo`.

```csharp
objeto.Set("vida", 100);
int vida = objeto.Get<int>("vida");
```

El setter de datos modifica inmediatamente el estado local. Cuando la sincronización está activa, Eco decide cómo propagar el cambio en función de si el cliente actual es el propietario y de si el objeto puede enviar en ese momento. Los cambios pueden quedar en cola hasta que el objeto vuelva a estar preparado para enviar. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs)

La sincronización de estos datos se explica con detalle en la sección de sincronización.

## Eventos del ciclo de vida [#eventos-del-ciclo-de-vida]

`Objeto` expone callbacks específicos para dos transiciones relevantes:

```text
onDestroy
onTransfer
```

`onDestroy` se ejecuta antes de las notificaciones normales de `OnDestroy` de Unity. `onTransfer` permite reaccionar cuando el objeto se traslada a otro canal. [https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs](https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs)

Esto permite separar el ciclo de vida de red del ciclo de vida del `GameObject` de Unity.

## Relación con Eco [#relación-con-eco]

Aunque el objeto encapsula gran parte del estado de red, la gestión global pertenece a `Eco`. Por ejemplo, la pertenencia a canales y la conexión se consultan a través de Eco, mientras que el objeto decide si su propio estado permite enviar.

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

La implementación de Eco conserva el modelo fundamental de TNet, pero no debe asumirse que futuras refactorizaciones mantendrán exactamente los mismos nombres o detalles internos.

## Referencias [#referencias]

<Card title="Objeto.cs · Eco" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs">
  Implementación actual de `Objeto`.
</Card>

<Card title="Componente.cs · Eco" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Componente.cs">
  Implementación de los componentes asociados a un objeto de red.
</Card>

<Card title="TNet · TNObject" href="https://github.com/tasharen/tnet/blob/master/Assets/TNet/Client/TNObject.cs">
  Implementación upstream utilizada como referencia histórica.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Documentación generada del repositorio actual de TNet para contrastar arquitectura y evolución.
</Card>
