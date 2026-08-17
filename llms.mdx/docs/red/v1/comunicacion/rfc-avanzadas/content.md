# RMR avanzadas (/docs/red/v1/comunicacion/rfc-avanzadas)



# RMR avanzadas [#rmr-avanzadas]

Las &#x2A;*RMR (Red Método Remoto)** son el mecanismo de Eco para ejecutar un método en otro extremo de la sesión. Una RMR describe una acción o notificación; no debe utilizarse como sustituto de un estado que necesita mantenerse sincronizado.

## RMR básica [#rmr-básica]

Una operación remota se invoca a través del `Objeto` que contiene el `Componente` correspondiente.

```csharp
public class MiUnidad : Componente
{
    public void RecibirDaño(int cantidad)
    {
        vida -= cantidad;
    }
}
```

La llamada transporta la identidad del objeto, el método remoto y sus parámetros según el protocolo de Eco.

## Destinatarios [#destinatarios]

La selección del destino forma parte de la semántica de la operación y debe hacerse explícitamente:

```text
Todos los jugadores
Todos excepto el emisor
Un jugador concreto
Un conjunto de jugadores
Solo estado persistente
```

## RMR persistente [#rmr-persistente]

Una RMR normal representa un evento puntual. Una RMR guardada representa una operación que además queda registrada como parte del estado necesario para reconstruir la sesión.

Esto es útil para:

* construir objetos o estado inicial;
* registrar una acción que nuevos jugadores necesitan reconstruir;
* almacenar una operación en el orden en que debe restaurarse.

No conviene guardar indiscriminadamente todas las operaciones: el historial persistente debe ser pequeño y determinista.

## RMR sin eco al cliente [#rmr-sin-eco-al-cliente]

Eco conserva el concepto heredado `NoneSaved`: una operación puede quedar almacenada para futuras reconstrucciones sin reenviarse como evento inmediato a los clientes actuales.

## Esperar confirmación [#esperar-confirmación]

Una operación dependiente puede necesitar esperar a que una operación previa haya sido procesada. El material upstream denomina `WaitForBounceBack` a uno de estos patrones. En Eco debe entenderse como coordinación de red y no como un bloqueo del hilo principal.

## RMR frente a sincronización [#rmr-frente-a-sincronización]

| Necesidad                              | Mecanismo                         |
| -------------------------------------- | --------------------------------- |
| Ejecutar una acción una vez            | RMR                               |
| Notificar un evento                    | RMR                               |
| Mantener una propiedad sincronizada    | `Set` / sincronización            |
| Restaurar estado para nuevos jugadores | datos persistentes / RMR guardada |
| Enviar una orden a un jugador concreto | RMR con destinatario explícito    |

## Errores habituales [#errores-habituales]

No uses una RMR para enviar la posición de una unidad cada frame si lo que realmente necesitas es sincronizar estado. Tampoco asumas que invocar una RMR convierte al emisor en propietario del objeto: la autoridad debe comprobarse según el flujo de la operación.

## Relación con TNet [#relación-con-tnet]

| Eco   | TNet  |
| ----- | ----- |
| `RMR` | `RFC` |
| `RCR` | `RCC` |
| `RCL` | `LCR` |

Las siglas de TNet se conservan aquí únicamente para facilitar la migración.

## Referencias [#referencias]

<Card title="RMR" href="/docs/red/v1/comunicacion/rmr">
  Referencia normativa de Red Método Remoto.
</Card>

<Card title="RCR" href="/docs/red/v1/comunicacion/rcr">
  Creación remota de objetos.
</Card>

<Card title="Destinatarios" href="/docs/red/v1/comunicacion/destinatarios">
  Selección de receptores y alcance de una operación.
</Card>
