# Destinatarios (/docs/red/v1/comunicacion/destinatarios)



# Destinatarios [#destinatarios]

Enviar un mensaje no significa necesariamente enviarlo a todos los participantes. Eco permite elegir el ámbito de destinatarios según el tipo de comunicación y el estado que se quiere mantener.

<Callout title="El destinatario forma parte del contrato" type="info">
  Antes de enviar un mensaje conviene decidir quién necesita conocerlo: el propietario, los demás jugadores, todo el canal o también los jugadores que entren posteriormente.
</Callout>

## Contexto de canal [#contexto-de-canal]

Los mensajes asociados a objetos se interpretan dentro de un `Canal`. El canal determina qué jugadores forman parte del ámbito de difusión.

```text
Canal
├── Jugador A
├── Jugador B
└── Jugador C
```

A partir de ese contexto, Eco puede excluir al emisor, incluirlo, conservar el mensaje para futuros participantes o dirigirlo a un participante concreto.

## Objetivos habituales [#objetivos-habituales]

| Objetivo conceptual | Uso                                                           |
| ------------------- | ------------------------------------------------------------- |
| `Todos`             | Todos los jugadores del canal.                                |
| `Otros`             | Todos salvo el emisor.                                        |
| Propietario         | El jugador que posee el objeto.                               |
| Persistente         | También debe estar disponible para participantes posteriores. |
| Jugador concreto    | Un único participante identificado.                           |

Los nombres exactos disponibles dependen de la API de `Objetivo` y del método utilizado para enviar el mensaje.

## Propietario frente a canal [#propietario-frente-a-canal]

La propiedad y la visibilidad son conceptos diferentes:

```text
Objeto
├── Propietario → Jugador A
└── Visible para → A, B, C
```

Que un objeto tenga propietario no significa que únicamente ese jugador pueda recibir sus actualizaciones.

## Comunicación persistente [#comunicación-persistente]

Un mensaje marcado para persistir puede formar parte del estado que Eco mantiene asociado al canal u objeto. Esto resulta útil cuando el contenido debe estar disponible también para jugadores que todavía no estaban conectados cuando ocurrió la comunicación.

No debe utilizarse persistencia sólo porque un mensaje se considere importante: debe existir una necesidad real de reconstruir ese estado posteriormente.

## Elección práctica [#elección-práctica]

```text
¿Quién necesita el mensaje?
        │
        ├── Sólo un jugador ───────► jugador concreto
        │
        ├── Propietario ───────────► propietario
        │
        ├── Todos ahora ───────────► todos / otros
        │
        └── Ahora + futuros ───────► persistente
```

## Relación con TNet [#relación-con-tnet]

Eco conserva el modelo de objetivos de TNet, pero utiliza la nomenclatura propia del proyecto. Las equivalencias deben comprobarse siempre contra la implementación actual de `Eco` y `Objeto`.

## Referencias [#referencias]

<Card title="Eco" href="https://github.com/Nervelink/eco">
  Implementación actual.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Referencia generada para contrastar el diseño upstream.
</Card>
