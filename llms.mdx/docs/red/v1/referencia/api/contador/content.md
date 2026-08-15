# Contador (/docs/red/v1/referencia/api/contador)



# Contador [#contador]

`Contador` representa un valor numérico limitado por `min` y `max` que cambia automáticamente según una tasa por segundo.

Es especialmente útil para recursos o estados que evolucionan con el tiempo y deben reconstruirse sin almacenar cada instante intermedio.

## Modelo [#modelo]

```text
valor almacenado
      +
  tiempo transcurrido × rate
      ↓
valor actual
      ↓
[min, max]
```

La propiedad `value` calcula el valor actual usando `Eco.TiempoServidorMS` en el runtime de Eco.

## Configuración [#configuración]

```csharp
var energia = new Contador(
    value: 100,
    rate: -5,
    min: 0,
    max: 100
);
```

Cada segundo la energía disminuye aproximadamente cinco unidades hasta alcanzar `min`.

## Propiedades [#propiedades]

| Miembro       | Función                        |
| ------------- | ------------------------------ |
| `min`         | Límite inferior.               |
| `max`         | Límite superior.               |
| `rate`        | Cambio por segundo.            |
| `value`       | Valor actual calculado.        |
| `storedValue` | Valor almacenado internamente. |

## Tiempo de servidor [#tiempo-de-servidor]

El contador utiliza el reloj del servidor, no `Time.deltaTime`, para calcular su evolución. Esto es importante cuando el valor forma parte de un estado compartido.

```csharp
double energiaActual = energia.value;
```

## Porcentaje de duración [#porcentaje-de-duración]

`GetPercent(delta)` calcula qué fracción del intervalo permanece antes de que el cambio alcance un límite.

```csharp
double porcentaje = energia.GetPercent(1.0);
```

## Serialización [#serialización]

`Contador` implementa tanto `IBinarySerializable` como `IDataNodeSerializable`.

```text
Contador
├── min
├── max
├── rate
├── value
└── timestamp
```

Esto permite conservar el estado sin tener que escribir un valor nuevo cada frame.

## Cuándo utilizarlo [#cuándo-utilizarlo]

Es apropiado para:

* recursos que regeneran o consumen valor;
* temporizadores cuantificados como estado;
* acumuladores limitados;
* estados persistentes que evolucionan entre dos observaciones.

No es un sustituto de la sincronización continua cuando el gameplay exige una simulación determinista por tick.

## Referencias [#referencias]

<Card title="Serialización" href="/docs/red/v1/referencia/api/serializacion">
  Interfaces de serialización utilizadas por `Contador`.
</Card>

<Card title="Código fuente" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Comun/Contador.cs">
  Implementación actual.
</Card>
