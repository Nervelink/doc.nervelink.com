# Objetivo (/docs/red/v1/referencia/api/objetivo)



# Objetivo [#objetivo]

`Objetivo` define el ámbito de distribución de un mensaje y, en los objetivos persistentes, si el servidor debe conservarlo para participantes posteriores.

| Objetivo           | Receptor                     | Persistencia |
| ------------------ | ---------------------------- | ------------ |
| `Todos`            | Todos los jugadores actuales | No           |
| `TodosPersistente` | Todos los actuales           | Sí           |
| `Otros`            | Todos excepto el emisor      | No           |
| `OtrosPersistente` | Todos excepto el emisor      | Sí           |
| `Host`             | Host del canal               | No           |
| `Difusion`         | Canal con control anti-spam  | No           |
| `Admin`            | Administradores              | No           |
| `SoloPersistente`  | Nadie inmediatamente         | Sí           |

## Ejemplo [#ejemplo]

```csharp
ero.Send("ActualizarEstado", Objetivo.Otros, estado);
```

Para conservar el estado:

```csharp
ero.Send("ActualizarEstado", Objetivo.TodosPersistente, estado);
```

Para guardar el estado sin emitirlo inmediatamente:

```csharp
ero.Send("ActualizarEstado", Objetivo.SoloPersistente, estado);
```

<Callout title="Persistencia no significa guardado de partida" type="info">
  Que un objetivo sea persistente significa que el servidor conserva la operación para futuras reproducciones según el modelo de Eco. No convierte cualquier mensaje en un sustituto de un sistema de guardado de partida.
</Callout>

## Cómo elegir [#cómo-elegir]

```text
¿El mensaje es un evento inmediato?
 ├── Sí → Todos / Otros
 │
 └── ¿Debe reconstruirse para jugadores futuros?
      ├── Sí → TodosPersistente / OtrosPersistente / SoloPersistente
      └── No → Todos / Otros
```

## Código fuente [#código-fuente]

<Card title="Objetivo.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Comun/Objetivo.cs">
  Definición actual de los objetivos de envío.
</Card>

<Card title="Destinatarios" href="/docs/red/v1/comunicacion/destinatarios">
  Explicación conceptual del ámbito de comunicación.
</Card>
