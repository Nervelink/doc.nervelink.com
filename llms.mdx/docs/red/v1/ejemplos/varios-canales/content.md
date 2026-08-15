# Varios canales (/docs/red/v1/ejemplos/varios-canales)



# Varios canales [#varios-canales]

Eco permite que una misma conexión participe en varios canales al mismo tiempo. Este comportamiento es especialmente importante en arquitecturas donde el jugador mantiene un contexto persistente y, simultáneamente, participa en una partida, chat, lobby o sesión secundaria.

<Callout title="No uses idUltimoCanal como contexto global" type="warn">
  `Eco.idUltimoCanal` existe como ayuda de compatibilidad y para determinados callbacks. Cuando un jugador está en varios canales, especifica siempre el `idCanal` de la operación.
</Callout>

## Modelo [#modelo]

```text
Una conexión
│
├── Canal 10 → Mundo
├── Canal 20 → Partida
└── Canal 30 → Chat
```

## Entrar en varios canales [#entrar-en-varios-canales]

```csharp
Eco.UnirseACanal(10, "Mundo", true, salirDeActual: false);
Eco.UnirseACanal(20, "Arena", false, salirDeActual: false);
```

Comprueba cada canal por separado:

```csharp
bool mundo = Eco.EstaEnCanal(10);
bool partida = Eco.EstaEnCanal(20);
Canal canalPartida = Eco.ObtenerCanal(20);
```

## Datos por canal [#datos-por-canal]

```csharp
Eco.EstablecerDatosCanal(10, "Estado/Clima", "Noche");
Eco.EstablecerDatosCanal(20, "Estado/Ronda", 4);
```

Nunca asumas que `ObtenerDatosCanal` se refiere al último canal utilizado: recibe explícitamente el ID.

## Objetos por canal [#objetos-por-canal]

Los objetos tienen su propio `channelID`.

```csharp
Objeto objetoMundo = ...;
Objeto objetoPartida = ...;

Debug.Assert(objetoMundo.channelID == 10);
Debug.Assert(objetoPartida.channelID == 20);
```

Las operaciones de instanciación y envío deben utilizar el canal del objeto o el canal explícito del contexto.

## Salir de un canal [#salir-de-un-canal]

```csharp
Eco.SalirDeCanal(20);

if (Eco.EstaEnCanal(10))
{
    // El mundo sigue activo.
}
```

La conexión continúa activa y el cliente puede seguir participando en los demás canales.

## Patrón recomendado [#patrón-recomendado]

<Steps>
  <Step>
    ### Mantén el contexto explícito [#mantén-el-contexto-explícito]

    Guarda el ID del canal en el sistema que representa ese contexto (`mundo`, `partida`, `chat`, etc.).
  </Step>

  <Step>
    ### Pasa el canal a las operaciones [#pasa-el-canal-a-las-operaciones]

    Evita depender de variables globales como `idUltimoCanal` para decidir dónde se ejecuta una operación.
  </Step>

  <Step>
    ### Mantén los objetos ligados a su canal [#mantén-los-objetos-ligados-a-su-canal]

    Usa `objeto.channelID` como fuente de verdad para operaciones específicas de una entidad.
  </Step>
</Steps>

## Relación con TNet [#relación-con-tnet]

La documentación histórica de TNet ya contemplaba múltiples canales, pero Eco lo incorpora como una capacidad fundamental del runtime actual y la expone mediante su fachada `Eco`.

<Card title="Canales" href="/docs/red/v1/modelo/canales">
  Modelo completo de canales y pertenencia.
</Card>

<Card title="Eco" href="/docs/red/v1/referencia/api/eco">
  API de la fachada para trabajar con varios canales.
</Card>
