# RFC avanzadas (/docs/red/v1/comunicacion/rfc-avanzadas)



# RFC avanzadas [#rfc-avanzadas]

Las RFC (Remote Function Calls) son el mecanismo principal de Eco para ejecutar una función en otro extremo de la sesión. Una RFC describe una acción o notificación; no debe utilizarse como sustituto de un estado que necesita mantenerse sincronizado.

## RFC básica [#rfc-básica]

Una función marcada como RFC puede ser invocada a través del `Objeto` que contiene el componente:

```csharp
[RFC]
void RecibirDaño(int cantidad)
{
    vida -= cantidad;
}
```

La llamada transporta la identidad del objeto, el identificador o nombre de la RFC y sus parámetros.

## Destinatarios [#destinatarios]

Eco hereda el modelo de objetivos de TNet. Entre los casos habituales están:

```text
Todos los jugadores
Todos excepto el emisor
Un jugador concreto
Un conjunto de jugadores
Solo el estado persistente
```

La selección del destino forma parte de la semántica de la llamada y debe hacerse explícitamente.

## RFC persistente [#rfc-persistente]

Una RFC normal representa un evento puntual. Una RFC guardada representa una operación que además queda registrada en el estado del canal para que pueda reproducirse cuando corresponda.

Esto es útil para:

* construir objetos o estado inicial;
* registrar una acción que nuevos jugadores necesitan reconstruir;
* almacenar una operación en el orden en que debe restaurarse.

No conviene guardar indiscriminadamente todas las acciones: el historial persistente debe ser suficientemente pequeño y determinista.

## RFC sin eco al cliente [#rfc-sin-eco-al-cliente]

TNet incluye un modo `NoneSaved` para almacenar una RFC en servidor sin reenviarla a los clientes actuales. Eco conserva la idea: permite guardar datos para futuros participantes sin convertirlos en un evento inmediato para todos.

## Esperar confirmación [#esperar-confirmación]

El flujo de red puede necesitar esperar a que llamadas previas hayan terminado antes de continuar con una operación dependiente. TNet documenta `WaitForBounceBack` para este escenario, y Eco debe tratar este patrón como una herramienta de coordinación, no como un bloqueo del hilo principal.

## RFC frente a sincronización [#rfc-frente-a-sincronización]

| Necesidad                              | Mecanismo                         |
| -------------------------------------- | --------------------------------- |
| Ejecutar una acción una vez            | RFC                               |
| Notificar un evento                    | RFC                               |
| Mantener una propiedad sincronizada    | `Set` / sincronización            |
| Restaurar estado para nuevos jugadores | datos persistentes / RFC guardada |
| Enviar una orden a un jugador concreto | RFC con destinatario explícito    |

## Errores habituales [#errores-habituales]

No uses una RFC para enviar la posición de una unidad cada frame si lo que realmente quieres es sincronizar su estado. Tampoco asumas que llamar una RFC significa que el emisor es necesariamente el propietario del objeto: la autoridad debe comprobarse según el flujo de la operación.

## Referencias [#referencias]

<Card title="Comunicación" href="/docs/red/v1/comunicacion">
  Modelo general de comunicación de Eco.
</Card>

<Card title="Destinatarios" href="/docs/red/v1/comunicacion/destinatarios">
  Selección de receptores y alcance de una llamada.
</Card>

<Card title="TNet upstream" href="https://github.com/tasharen/tnet">
  Implementación de referencia heredada.
</Card>
