# Jugador (/docs/red/v1/referencia/api/jugador)



# Jugador [#jugador]

`Jugador` representa a un participante de la sesión de red. Incluye una identidad asignada por el servidor y un `Nodo` de datos asociado.

## Identidad [#identidad]

```csharp
int id = jugador.id;
string nombre = jugador.name;
```

El `id` es asignado por el servidor y es la identidad estable que debe utilizarse al referirse a un jugador durante una sesión.

## Comparación [#comparación]

Eco define la igualdad de `Jugador` en función de su identificador. Esto permite comparar referencias procedentes de distintas partes del cliente sin depender de que sean la misma instancia de C#.

```csharp
if (jugador == Eco.Jugador)
{
    // Es el jugador local.
}
```

## Datos [#datos]

`Jugador` hereda de `DataNodeContainer`, por lo que dispone de:

```csharp
jugador.Set("Perfil/Nivel", 10);
int nivel = jugador.Get<int>("Perfil/Nivel", 1);
```

Para el jugador local, normalmente utiliza la fachada:

```csharp
Eco.EstablecerDatosJugador("Perfil/Nivel", 10);
```

## Aliases [#aliases]

Los aliases permiten asociar identificadores adicionales al jugador, por ejemplo un Steam ID, ID de cuenta o identificador de plataforma.

```csharp
jugador.AddAlias(steamId);
bool coincide = jugador.HasAlias(steamId);
```

Los aliases pueden utilizarse por el servidor en sus mecanismos de administración y bloqueo.

<Callout title="ID frente a alias" type="info">
  El `id` de `Jugador` pertenece a la sesión de Eco. Un alias identifica externamente al usuario y no debe sustituir al ID de red como referencia de gameplay durante una sesión.
</Callout>

## Eventos [#eventos]

Los cambios de jugadores pueden observarse desde Eco mediante eventos como:

```csharp
Eco.alEntrarJugador += OnPlayerJoin;
Eco.alSalirJugador += OnPlayerLeave;
Eco.alRenombrarJugador += OnPlayerRename;
```

## API relacionada [#api-relacionada]

<Card title="Eco" href="/docs/red/v1/referencia/api/eco">
  Acceso al jugador local y búsqueda de jugadores remotos.
</Card>

<Card title="Canal" href="/docs/red/v1/referencia/api/canal">
  Colección de jugadores que participan en el canal.
</Card>

<Card title="Datos del jugador" href="/docs/red/v1/guias/sincronizar-jugador">
  Workflow práctico para sincronizar `playerData`.
</Card>

<Card title="Jugador.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Comun/Jugador.cs">
  Implementación actual.
</Card>
