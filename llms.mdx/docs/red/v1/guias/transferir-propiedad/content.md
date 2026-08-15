# Transferir la propiedad de un objeto (/docs/red/v1/guias/transferir-propiedad)



# Transferir la propiedad de un objeto [#transferir-la-propiedad-de-un-objeto]

Los objetos dinámicos de Eco tienen un propietario que determina quién puede actuar como autoridad sobre ellos. La propiedad puede cambiarse durante la vida del objeto siempre que el nuevo propietario pertenezca al mismo canal.

<Callout title="Importante" type="warn">
  Los objetos estáticos no permiten cambiar de propietario. La transferencia de propiedad está destinada a objetos dinámicos.
</Callout>

## Identificar al propietario [#identificar-al-propietario]

`Objeto` expone tres formas relacionadas para consultar la propiedad:

```csharp
objeto.owner
objeto.ownerID
objeto.isMine
```

`owner` devuelve el `Jugador`, `ownerID` su identificador y `isMine` indica si el objeto pertenece al jugador local. fileciteturn212file0

## Transferir a otro jugador [#transferir-a-otro-jugador]

La propiedad puede asignarse mediante `owner` o `ownerID`:

```csharp
objeto.owner = jugador;
```

También puede utilizarse el identificador:

```csharp
objeto.ownerID = jugador.id;
```

Eco valida que el objeto sea dinámico y que el jugador destino pertenezca al canal del objeto. Si la comprobación es válida, actualiza primero el propietario local y envía una solicitud `RequestSetOwner` al servidor. fileciteturn212file0 fileciteturn213file0

```text
Cliente
  │
  │ RequestSetOwner
  ▼
Servidor
  │
  ├── valida jugador
  ├── actualiza propiedad
  └── propaga el nuevo propietario
```

## Condición: mismo canal [#condición-mismo-canal]

No debe intentarse transferir un objeto a un jugador que no esté en su canal.

```text
Canal 10
├── Objeto 100
│   └── Propietario: Jugador A
├── Jugador A
└── Jugador B   ← destino válido

Canal 20
└── Jugador C   ← destino inválido
```

La implementación comprueba `Eco.EstaJugadorEnCanal(value, channelID)` antes de enviar la solicitud. fileciteturn213file0

## Transferencia a nadie [#transferencia-a-nadie]

Asignar `ownerID = 0` elimina el propietario explícito:

```csharp
objeto.ownerID = 0;
```

El protocolo permite este valor y Eco lo envía mediante `RequestSetOwner`. fileciteturn213file0

El significado exacto de un objeto sin propietario debe decidirse dentro de la arquitectura del juego; no debe confundirse automáticamente con "propiedad del servidor".

## Autoridad y diseño de gameplay [#autoridad-y-diseño-de-gameplay]

La propiedad debe formar parte del diseño de autoridad de la entidad. Un patrón habitual es:

```text
Objeto de jugador
    ↓
Propietario = jugador que lo controla

Objeto compartido
    ↓
Propietario = autoridad elegida por el juego

Objeto estático
    ↓
Propiedad no transferible
```

No conviene utilizar `isMine` como sustituto de todas las comprobaciones de autoridad. `isMine` sólo responde a si el propietario actual coincide con el jugador local.

## Propiedad y sincronización [#propiedad-y-sincronización]

Transferir propiedad no sustituye a la sincronización del estado. Son dos operaciones diferentes:

```text
Cambiar propietario
        │
        └── determina quién tiene autoridad

Sincronizar estado
        │
        └── distribuye los datos del objeto
```

Después de una transferencia, la lógica del nuevo propietario debe utilizar el sistema de sincronización y RFC apropiado para continuar actualizando el objeto.

## Relación con canales [#relación-con-canales]

La propiedad está asociada al canal del objeto. Por tanto, cambiar de canal y transferir propiedad son operaciones distintas:

```text
Transferencia
Objeto ───────────→ nuevo jugador

Cambio de canal
Objeto ───────────→ nuevo canal
```

La implementación de `Objeto` también expone `onTransfer` para notificar cuando un objeto está siendo transferido a otro canal. fileciteturn212file0

## Errores frecuentes [#errores-frecuentes]

### Intentar cambiar el propietario de un objeto estático [#intentar-cambiar-el-propietario-de-un-objeto-estático]

Eco rechaza esta operación porque los IDs estáticos no permiten cambiar de propietario. fileciteturn212file0

### Asignar un jugador de otro canal [#asignar-un-jugador-de-otro-canal]

El destino debe estar en el mismo canal que el objeto. La implementación comprueba esta condición antes de enviar la petición. fileciteturn213file0

### Suponer que `isMine` concede autoridad automáticamente [#suponer-que-ismine-concede-autoridad-automáticamente]

`isMine` describe la propiedad local; la lógica de autoridad del juego sigue siendo responsabilidad de la integración.

## Referencias [#referencias]

<Card title="Objeto" href="/docs/red/v1/modelo/objetos">
  Identidad, ownership, ciclo de vida y estado de los objetos de red.
</Card>

<Card title="Sincronizar una entidad" href="/docs/red/v1/guias/sincronizar-entidad">
  Cómo mantener el estado sincronizado después de establecer la autoridad.
</Card>

<Card title="Instanciar y destruir objetos" href="/docs/red/v1/guias/instanciar-destruir">
  Creación dinámica, IDs y destrucción de objetos.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream de la arquitectura heredada.
</Card>
