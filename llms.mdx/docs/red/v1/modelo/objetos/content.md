# Objetos (/docs/red/v1/modelo/objetos)



# Objetos [#objetos]

Un `Objeto` es la identidad de red asociada a un `GameObject`. Es la pieza que permite localizar una entidad dentro de un canal, conocer su propietario, recibir RFC y mantener datos sincronizados.

<Callout title="Objeto ≠ GameObject" type="info">
  El `GameObject` pertenece a Unity. `Objeto` aporta la identidad y el estado que hacen posible que esa entidad exista en la red.
</Callout>

## Anatomía de un objeto [#anatomía-de-un-objeto]

<Cards>
  <Card title="Identidad">
    `id`, `channelID` y `uid` permiten localizar el objeto dentro del contexto de red.
  </Card>

  <Card title="Ownership">
    `owner`, `ownerID` e `isMine` determinan quién tiene la autoridad lógica sobre el objeto.
  </Card>

  <Card title="Estado">
    `Nodo`, `Set` y `Get` permiten mantener datos asociados a la entidad.
  </Card>

  <Card title="Componentes">
    Los `Componente` implementan el comportamiento de gameplay conectado al objeto.
  </Card>
</Cards>

## Identidad [#identidad]

Los objetos utilizan dos espacios principales de IDs:

| Tipo     | Rango              | Uso                                  |
| -------- | ------------------ | ------------------------------------ |
| Estático | `1`–`32767`        | Objetos presentes desde la escena    |
| Dinámico | `32768`–`16777215` | Objetos creados durante la ejecución |

Su identificador completo incorpora también el canal:

```text
uid = (channelID << 32) | id
```

Esto evita confundir objetos de diferentes canales que compartan el mismo `id` local.

## Ciclo de vida [#ciclo-de-vida]

```text
Prefab / escena
      ↓
Objeto creado
      ↓
Registro en Eco
      ↓
Operativo
      ↓
Transferencia / cambios de estado
      ↓
Destrucción
```

`hasBeenRegistered` indica que la creación terminó y se asignó el identificador correspondiente. `hasBeenDestroyed` indica que el objeto ya ha entrado en el proceso de destrucción.

## Ownership [#ownership]

```csharp
if (objeto.isMine)
{
    // El jugador local es el propietario.
}

int propietario = objeto.ownerID;
Jugador jugador = objeto.owner;
```

La propiedad no equivale a visibilidad:

```text
Objeto
├── Propietario → Jugador A
└── Visible para → A, B, C
```

Consulta [Transferir la propiedad](/docs/red/v1/guias/transferir-propiedad) para el workflow completo.

<Callout title="Objetos estáticos" type="warn">
  Eco no permite cambiar el propietario de un objeto estático de la misma forma que el de un objeto dinámico. Diseña las entidades que necesitarán ownership transferible como objetos dinámicos.
</Callout>

## Datos del objeto [#datos-del-objeto]

```csharp
objeto.Set("vida", 100);
int vida = objeto.Get<int>("vida");
```

`Set` actualiza el dato local inmediatamente. Cuando se solicita sincronización, Eco comprueba si el objeto puede enviar, quién es el propietario y si la operación debe atravesar una cola temporal.

```text
Set()
 │
 ├── Actualiza estado local
 │
 └── ¿Puede enviar?
       │
       ├── Sí → sincronización
       │
       └── No → cola pendiente
```

Consulta [Sincronizar una entidad](/docs/red/v1/guias/sincronizar-entidad) para ver este patrón en un workflow real.

## Componentes [#componentes]

Un `Componente` busca el `Objeto` en su jerarquía de Unity y ofrece métodos de conveniencia para comunicación, datos, instanciación y destrucción.

```text
GameObject
├── Objeto
├── Componente de movimiento
├── Componente de combate
└── Componente de inventario
```

El código de gameplay debería vivir normalmente en componentes, mientras que `Objeto` concentra la identidad de red.

## Capacidad de envío [#capacidad-de-envío]

`PuedeEnviar` no significa simplemente “hay conexión”. El objeto debe tener un ID válido, no estar destruido, pertenecer a un canal utilizable y no estar en una transición de entrada al canal.

Esto resulta especialmente importante durante:

* entrada a canales;
* cambios de escena;
* creación dinámica;
* transferencia entre canales;
* destrucción.

## Destrucción y transferencia [#destrucción-y-transferencia]

`onDestroy` y `onTransfer` permiten observar eventos del ciclo de vida de red:

```csharp
objeto.onDestroy += () =>
{
    // Preparar la lógica local antes de la destrucción de red.
};

objeto.onTransfer += (nuevoCanal, nuevoID) =>
{
    // Actualizar referencias dependientes del contexto.
};
```

Consulta [Instanciar y destruir objetos](/docs/red/v1/guias/instanciar-destruir) y [Transferir un objeto entre canales](/docs/red/v1/guias/transferir-objeto-canal).

## Qué no debe hacer un objeto [#qué-no-debe-hacer-un-objeto]

<Callout title="Mantén las responsabilidades separadas" type="idea">
  `Objeto` no debería convertirse en tu sistema de gameplay. Utilízalo como identidad, ownership, estado y puerta de entrada a la comunicación; coloca reglas de gameplay en `Componente`.
</Callout>

## Correspondencia con TNet [#correspondencia-con-tnet]

| Eco          | TNet          | Función                        |
| ------------ | ------------- | ------------------------------ |
| `Objeto`     | `TNObject`    | Identidad y estado de red      |
| `Componente` | `TNBehaviour` | Comportamiento asociado        |
| `owner`      | `owner`       | Jugador propietario            |
| `ownerID`    | `ownerID`     | Identificador del propietario  |
| `isMine`     | `isMine`      | Propiedad local                |
| `uid`        | `uid`         | Contexto completo de identidad |

## Referencias [#referencias]

<Cards>
  <Card title="Componente" href="/docs/red/v1/modelo/objetos">
    Integración de `Objeto` con los `MonoBehaviour` de Unity.
  </Card>

  <Card title="Sincronización" href="/docs/red/v1/comunicacion/sincronizacion">
    Cómo se propaga el estado de una entidad.
  </Card>

  <Card title="Objeto.cs · Eco" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Objeto.cs">
    Implementación actual de `Objeto`.
  </Card>

  <Card title="Componente.cs · Eco" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/Componente.cs">
    Implementación actual de `Componente`.
  </Card>
</Cards>
