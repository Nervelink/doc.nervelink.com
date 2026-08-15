# Referencia API (/docs/red/v1/referencia/api)



# Referencia API [#referencia-api]

Esta sección documenta la API que utilizarás directamente desde el código del juego. A diferencia de las guías, aquí el objetivo es responder rápidamente a tres preguntas: qué representa un tipo, qué operaciones expone y cuándo debe utilizarse.

<Callout title="La API de Eco es más amplia que TNet histórico" type="info">
  Eco conserva la base de TNet, pero la fachada actual incorpora nombres, utilidades y operaciones propias de Nervelink. La referencia debe leerse junto con el código actual de `Nervelink/eco`.
</Callout>

## Punto de entrada habitual [#punto-de-entrada-habitual]

En la mayoría de los sistemas de gameplay no necesitas instanciar `Eco` ni trabajar directamente con `ClienteJuego`.

```csharp
Eco.Conectar("127.0.0.1", 5127);
Eco.UnirseACanal(1, "Game", false, 4, null);
Eco.EstaConectado;
Eco.EstaEnCanal(1);
```

Para operaciones avanzadas, Eco expone el cliente interno mediante `Eco.Cliente`.

## Cómo leer esta referencia [#cómo-leer-esta-referencia]

<Card title="Eco" href="/docs/red/v1/referencia/api/eco">
  Fachada principal y API estática de la red.
</Card>

<Card title="Objeto" href="/docs/red/v1/referencia/api/objeto">
  Identidad de red, ownership, estado y ciclo de vida.
</Card>

<Card title="Componente" href="/docs/red/v1/referencia/api/componente">
  Base para comportamientos de Unity asociados a un `Objeto`.
</Card>

<Card title="Canal" href="/docs/red/v1/referencia/api/canal">
  Estado, jugadores, objetos persistentes y RFC guardadas de un canal.
</Card>

<Card title="Jugador" href="/docs/red/v1/referencia/api/jugador">
  Identidad y datos sincronizados de un participante.
</Card>

<Card title="Objetivo" href="/docs/red/v1/referencia/api/objetivo">
  Destinatarios y persistencia del envío.
</Card>

<Card title="ClienteJuego" href="/docs/red/v1/referencia/api/cliente-juego">
  Implementación de la sesión cliente.
</Card>

<Card title="ServidorJuego" href="/docs/red/v1/referencia/api/servidor-juego">
  Implementación del runtime del servidor.
</Card>

## Fuente de verdad [#fuente-de-verdad]

La referencia API de este sitio es deliberadamente práctica. Para firmas completas, sobrecargas, atributos y detalles de implementación, utiliza el código fuente enlazado desde cada página.
