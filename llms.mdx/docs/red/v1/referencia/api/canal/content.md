# Canal (/docs/red/v1/referencia/api/canal)



# Canal [#canal]

`Canal` representa el ámbito de estado compartido en el servidor. Además de contener jugadores, mantiene información necesaria para reconstruir objetos y RFC persistentes.

## Propiedades principales [#propiedades-principales]

| Propiedad      | Función                                      |
| -------------- | -------------------------------------------- |
| `id`           | Identificador del canal.                     |
| `level`        | Nivel/escena asociado.                       |
| `password`     | Protección de entrada.                       |
| `isPersistent` | Mantiene el canal aunque quede vacío.        |
| `isClosed`     | Impide nuevas entradas.                      |
| `isLocked`     | Impide determinadas operaciones de guardado. |
| `isLeaving`    | Estado transitorio del cliente al salir.     |
| `playerLimit`  | Máximo de jugadores.                         |
| `jugadores`    | Jugadores presentes.                         |
| `rfcs`         | RFC persistentes registradas en el canal.    |
| `created`      | Objetos dinámicos guardados.                 |
| `destroyed`    | Objetos marcados como destruidos.            |
| `host`         | Anfitrión actual del canal.                  |

## Capacidad de entrada [#capacidad-de-entrada]

`isOpen` indica si el canal no está cerrado y todavía dispone de plazas.

```csharp
if (canal.isOpen)
{
    // Puede aceptar una entrada.
}
```

## Objetos dinámicos [#objetos-dinámicos]

Los objetos creados en runtime utilizan un espacio de IDs distinto al de los objetos de escena. `GetUniqueID()` genera identificadores dinámicos libres.

```text
1 ───────── 32767
   objetos estáticos

32768 ───── 16777215
   objetos dinámicos
```

Los objetos dinámicos se almacenan en `created` cuando deben persistir en el servidor.

## Propiedad al salir un jugador [#propiedad-al-salir-un-jugador]

Cuando un jugador abandona el canal, Eco revisa sus objetos dinámicos. Según el tipo de persistencia:

* algunos objetos se destruyen;
* otros transfieren la propiedad a otro jugador;
* los objetos persistentes permanecen asociados al canal.

Este comportamiento es una de las razones por las que `Canal` no puede tratarse sólo como una lista de jugadores.

## RFC guardadas [#rfc-guardadas]

`rfcs` contiene llamadas remotas persistentes asociadas al canal. Cuando una RFC guardada se actualiza, Eco puede moverla al final de la lista para conservar el orden de reconstrucción.

## Transferencia de objetos [#transferencia-de-objetos]

`TransferObject` mueve un objeto dinámico a otro canal, asigna un nuevo ID y mueve también sus RFC guardadas. Eco mantiene temporalmente un registro de redirección para absorber paquetes que todavía lleguen con el identificador anterior.

<Callout title="Sólo objetos dinámicos" type="warn">
  La transferencia entre canales sólo funciona con objetos creados dinámicamente. Los objetos estáticos de escena no pueden transferirse de esta forma.
</Callout>

## Persistencia y memoria [#persistencia-y-memoria]

Los canales vacíos pueden pasar a estado de reposo mediante `Sleep()` y recuperar sus datos con `Wake()`. Esto permite reducir el consumo de memoria del servidor manteniendo el estado persistente en una representación serializada.

```text
Canal activo
   ↓ jugadores salen
Canal vacío
   ↓ Sleep()
Datos comprimidos/serializados
   ↓ Wake()
Canal activo
```

## API relacionada [#api-relacionada]

<Card title="Canales" href="/docs/red/v1/modelo/canales">
  Modelo conceptual y uso desde el cliente.
</Card>

<Card title="Persistencia de canales" href="/docs/red/v1/persistencia/canales">
  Cómo se conserva el estado entre sesiones.
</Card>

<Card title="Canal.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Comun/Canal.cs">
  Implementación actual de `Canal`.
</Card>
