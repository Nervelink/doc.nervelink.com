# Referencia API (/docs/red/v1/referencia/api)



# Referencia API [#referencia-api]

La referencia API responde a una pregunta distinta de las guías: **qué expone cada tipo y cómo utilizarlo correctamente**. Cada página debe indicar la firma relevante, el estado que modifica, las restricciones y un ejemplo mínimo.

<Callout title="Fuente normativa" type="info">
  La documentación resume el comportamiento de Eco, pero la implementación de `Nervelink/eco` es la fuente final para firmas, sobrecargas, atributos y detalles que dependan de la versión del código.
</Callout>

## Mapa rápido [#mapa-rápido]

```text
Eco
├── ClienteJuego
│   ├── Canales
│   ├── Jugadores
│   └── Objetos
│       └── Componente
│
├── ServidorJuego
│   ├── Servidor
│   ├── Canales
│   └── Jugadores
│
└── Infraestructura
    ├── Objetivo
    ├── Paquete
    ├── Buffer
    ├── Nodo
    └── IConnection
```

## Por dónde empezar [#por-dónde-empezar]

<Cards>
  <Card title="Eco" href="/docs/red/v1/referencia/api/eco">
    Entrada habitual para conexión, canales, datos, objetos y diagnóstico.
  </Card>

  <Card title="Objeto" href="/docs/red/v1/referencia/api/objeto">
    Identidad, ownership, datos y ciclo de vida de una entidad de red.
  </Card>

  <Card title="Componente" href="/docs/red/v1/referencia/api/componente">
    Integración entre `MonoBehaviour` y `Objeto`.
  </Card>

  <Card title="ClienteJuego" href="/docs/red/v1/referencia/api/cliente-juego">
    Runtime de cliente para integraciones avanzadas.
  </Card>
</Cards>

## Convención de lectura [#convención-de-lectura]

Cada referencia utiliza este orden:

| Bloque        | Qué responde                                       |
| ------------- | -------------------------------------------------- |
| Identidad     | Qué representa el tipo y qué estado posee          |
| Propiedades   | Qué puedes consultar                               |
| Métodos       | Qué operaciones puedes solicitar                   |
| Restricciones | Cuándo la operación puede fallar o estar bloqueada |
| Ejemplo       | Uso mínimo y copiables                             |
| Relación      | Qué otros tipos intervienen                        |
| Fuente        | Dónde vive la implementación                       |

## API de alto nivel [#api-de-alto-nivel]

`Eco`, `Objeto` y `Componente` cubren la mayor parte del gameplay. Son preferibles porque mantienen autoridad, canales, serialización y ciclo de vida dentro de las reglas de Eco.

## API de infraestructura [#api-de-infraestructura]

Cuando una integración necesita acceder al protocolo, utiliza [APIs de bajo nivel](/docs/red/v1/referencia/api/bajo-nivel). Ahí entran `Paquete`, `Buffer`, handlers personalizados, `IConnection` y métricas.

<Callout title="No conviertas el gameplay en protocolo" type="warn">
  Manipular `Buffer` directamente para implementar una acción normal aumenta el acoplamiento y hace más difícil mantener compatibilidad. Utiliza RFC, estado y la API de objetos siempre que sea suficiente.
</Callout>

## Comparación con TNet [#comparación-con-tnet]

La referencia mantiene enlaces de equivalencia para facilitar la migración desde TNet, pero la firma que debes utilizar es siempre la de Eco:

```text
TNet                     Eco
────────────────────────────────────
TNManager             →  Eco
TNObject              →  Objeto
TNBehaviour           →  Componente
Channel               →  Canal
Player                →  Jugador
Packet                →  Paquete
DataNode              →  Nodo
TNServerInstance      →  Servidor / ServidorJuego
GameClient            →  ClienteJuego
```

<Card title="Equivalencias Eco ↔ TNet" href="/docs/red/v1/referencia/equivalencias">
  Tabla de nombres y diferencias de comportamiento.
</Card>
