# Objetivo (/docs/red/v1/referencia/api/objetivo)



# Objetivo [#objetivo]

`Objetivo` determina a quién llega una comunicación y si esa comunicación debe conservarse en el servidor para participantes futuros.

```csharp
ero.Send("Abrir", Objetivo.Todos);
ero.Send("Actualizar", Objetivo.OtrosPersistente);
```

## Modos disponibles [#modos-disponibles]

| Objetivo           | Receptor                                         | Persistencia | Uso habitual                           |
| ------------------ | ------------------------------------------------ | ------------ | -------------------------------------- |
| `Todos`            | Todos los jugadores actuales, incluido el emisor | No           | Evento visual inmediato                |
| `TodosPersistente` | Todos los jugadores actuales                     | Sí           | Estado que debe reconstruirse          |
| `Otros`            | Todos excepto el emisor                          | No           | El local ya aplicó el efecto           |
| `OtrosPersistente` | Todos excepto el emisor                          | Sí           | Estado global sin duplicar local       |
| `Host`             | Host del canal                                   | No           | Compatibilidad o lógica delegada       |
| `Difusion`         | Todos según su mecanismo de difusión             | No           | Mensajes frecuentes                    |
| `Admin`            | Administradores                                  | No           | Diagnóstico y administración           |
| `SoloPersistente`  | Nadie inmediatamente                             | Sí           | Registrar estado para futuras entradas |

<Callout title="Objetivo no decide autoridad" type="warn">
  El destinatario determina quién recibe el mensaje. La autoridad sobre un `Objeto` es una cuestión diferente y debe resolverse con ownership y `PuedeEnviar`.
</Callout>

## Persistencia [#persistencia]

Los objetivos con `Persistente` no significan simplemente “enviar varias veces”. El servidor conserva la operación para que los jugadores que entren posteriormente puedan reconstruir el estado correspondiente.

Esto resulta útil para:

* puertas abiertas;
* objetos destruidos;
* activación de eventos;
* configuración persistente de un canal.

## Ejemplo [#ejemplo]

Supón que un jugador activa una palanca y todos los jugadores deben verla activa:

```csharp
ero.Send("Activar", Objetivo.TodosPersistente);
```

Si la animación local ya se ejecuta antes del envío y el emisor no necesita recibir la RFC de nuevo:

```csharp
ero.Send("Activar", Objetivo.OtrosPersistente);
```

## SoloPersistente [#solopersistente]

`SoloPersistente` permite registrar la operación en el servidor sin enviarla inmediatamente a los jugadores actuales.

Es útil cuando el estado se utilizará como parte de la reconstrucción futura y los jugadores actuales ya conocen el estado por otro mecanismo.

## Objetivo y RFC [#objetivo-y-rfc]

No todas las RFC deberían utilizar el mismo objetivo. La decisión depende de la semántica:

```text
¿Es una acción remota?
        ↓
       RFC
        ↓
¿Quién necesita recibirla?
        ↓
Objetivo
        ↓
¿Debe reconstruirse después?
        ↓
Persistente / no persistente
```

<Card title="RFC" href="/docs/red/v1/comunicacion/rfc">
  Cómo definir y enviar acciones remotas.
</Card>

<Card title="Paquete" href="/docs/red/v1/referencia/api/paquete">
  Representación de bajo nivel de una comunicación.
</Card>

<Card title="Objetivo.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Comun/Objetivo.cs">
  Definición actual de los destinatarios.
</Card>
