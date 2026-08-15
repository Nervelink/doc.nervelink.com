# RFC (/docs/red/v1/ejemplos/rfc)



# RFC [#rfc]

Una RFC permite ejecutar un método de un `Componente` asociado a un `Objeto` de red desde otra instancia de la sesión.

## Declarar la función [#declarar-la-función]

```csharp
public class Puerta : Componente
{
    [RMR]
    void Abrir()
    {
        animator.SetBool("Abierta", true);
    }
}
```

El atributo registra el método para que Eco pueda identificarlo y ejecutarlo cuando llegue el mensaje.

## Enviar [#enviar]

El método se invoca sobre el `Objeto` o mediante las ayudas disponibles desde `Componente`.

```csharp
ero.Send("Abrir", Objetivo.Otros);
```

Para incluir datos:

```csharp
ero.Send("RecibirDaño", Objetivo.Otros, 25);
```

## Elegir destinatarios [#elegir-destinatarios]

```csharp
// Todos, incluido el emisor
ero.Send("MostrarEfecto", Objetivo.Todos);

// Todos excepto el emisor
ero.Send("MostrarEfecto", Objetivo.Otros);

// Todos excepto el emisor, guardado para jugadores posteriores
ero.Send("EstadoActualizado", Objetivo.OtrosPersistente);
```

Consulta [Objetivo](/docs/red/v1/referencia/api/objetivo) para la tabla completa.

## RFC frente a estado [#rfc-frente-a-estado]

Una RFC debe representar una acción o evento:

```text
"Atacar"
"Abrir"
"MostrarExplosión"
```

El estado debe representarse como datos:

```text
vida = 75
puertaAbierta = true
munición = 12
```

<Callout title="Diseña alrededor del resultado" type="info">
  Una buena prueba es preguntar qué debe conocer un jugador que entre cinco segundos después. Si la respuesta es “el resultado actual”, necesitas estado persistente; si es “la acción que acaba de ocurrir”, normalmente necesitas una RFC.
</Callout>

## Persistencia [#persistencia]

El modelo de Eco permite RFC guardadas en el servidor. Una RFC persistente no es simplemente un mensaje con `true`: pasa a formar parte del estado que puede reconstruirse posteriormente.

## Errores habituales [#errores-habituales]

### Método no encontrado [#método-no-encontrado]

Comprueba que el método está en un componente asociado al `Objeto` y que el atributo RMR/RFC utilizado pertenece a la API actual de Eco.

### Parámetros incompatibles [#parámetros-incompatibles]

Los parámetros deben ser serializables y coincidir con lo que espera la función receptora.

### Ejecutar lógica de servidor en el cliente [#ejecutar-lógica-de-servidor-en-el-cliente]

Una RFC ejecuta código en la instancia receptora. No la confundas con una validación server-authoritative.

## Fuente [#fuente]

<Card title="Ejemplo RFC" href="https://github.com/Nervelink/eco/tree/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Examples">
  El repositorio contiene los ejemplos heredados de RFC y sus escenas.
</Card>

<Card title="RFC avanzadas" href="/docs/red/v1/comunicacion/rfc-avanzadas">
  Persistencia, callbacks y patrones avanzados.
</Card>
