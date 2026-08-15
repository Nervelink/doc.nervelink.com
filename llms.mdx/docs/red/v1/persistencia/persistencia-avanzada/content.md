# Persistencia avanzada (/docs/red/v1/persistencia/persistencia-avanzada)



# Persistencia avanzada [#persistencia-avanzada]

La persistencia de Eco no consiste en hacer un `Save()` general de la escena. El servidor conserva determinadas operaciones y datos y puede reconstruir un canal a partir de ellos.

```text
Estado persistente
├── Datos del servidor
├── Datos del canal
├── Datos del jugador
├── Objetos persistentes
└── RFC guardadas
```

## Objetos persistentes [#objetos-persistentes]

Al crear un objeto puedes marcarlo como persistente:

```csharp
Eco.Instanciar(
    canalId,
    1,
    "MiPrefab",
    true,
    parametros);
```

La persistencia significa que el servidor conservará la información necesaria para reconstruir ese objeto cuando corresponda.

<Callout title="Persistencia no es sincronización continua" type="info">
  Un objeto persistente puede desaparecer visualmente de un cliente y reaparecer después. La persistencia describe cómo se reconstruye su existencia; la sincronización describe cómo se mantiene su estado durante la sesión.
</Callout>

## RFC persistentes [#rfc-persistentes]

Las RFC también pueden formar parte del estado persistente. Esto permite expresar el estado de un objeto como una secuencia de acciones reproducibles.

```text
Creación
  ↓
RFC de configuración
  ↓
RFC de estado
  ↓
Guardar
  ↓
Cliente nuevo
  ↓
Reproducir estado necesario
```

Existe además un patrón especialmente útil equivalente al antiguo `NoneSaved`: conservar la operación sin reenviarla inmediatamente a los clientes actuales.

Esto resulta apropiado para valores que pueden calcularse a partir del tiempo transcurrido, interpolación o estado base.

## Orden de las RFC guardadas [#orden-de-las-rfc-guardadas]

Cuando se actualiza una RFC persistente, el orden de las operaciones guardadas importa. El servidor mantiene el orden adecuado para que la reconstrucción produzca el mismo estado lógico.

## Canales persistentes [#canales-persistentes]

Un canal persistente puede sobrevivir aunque no queden jugadores conectados. Esto es distinto de un canal temporal:

| Tipo        | Último jugador sale | Estado                 |
| ----------- | ------------------- | ---------------------- |
| Temporal    | Se puede eliminar   | Se pierde con el canal |
| Persistente | Permanece           | Puede restaurarse      |

## Reducción de memoria [#reducción-de-memoria]

En servidores con muchos canales, mantener todas las representaciones activas puede ser costoso. El modelo heredado de TNet permite descargar el contenido de canales vacíos después de guardar su estado.

```text
Canal vacío
   ↓
Guardar
   ↓
Descargar estado activo
   ↓
Memoria reducida
   ↓
Nuevo jugador
   ↓
Recargar estado
```

El tiempo de carga aumenta a cambio de reducir memoria.

## Exportar e importar objetos [#exportar-e-importar-objetos]

El servidor puede exportar un grupo de objetos y recuperar posteriormente la información necesaria para recrearlo.

```csharp
Eco.ExportarObjetos(objetos, datos =>
{
    // Guardar los datos exportados.
});
```

Y posteriormente:

```csharp
Eco.ImportarObjetos(canalId, datos, ids =>
{
    // Objetos reconstruidos.
});
```

Esto es útil para plantillas complejas, vehículos configurados, grupos de entidades o prefabs cuyo estado inicial no conviene reconstruir manualmente.

## Qué no debe guardarse aquí [#qué-no-debe-guardarse-aquí]

No conviertas todo el estado de gameplay en RFC persistentes. Datos transitorios, efectos visuales o estados derivados suelen reconstruirse mejor desde una fuente persistente más pequeña.

<Callout title="Diseña el estado mínimo" type="idea">
  La mejor persistencia es la que necesita almacenar la menor cantidad de información para reconstruir el mismo resultado.
</Callout>

<Card title="Persistir una partida" href="/docs/red/v1/guias/persistir-una-partida">
  Workflow práctico de persistencia.
</Card>

<Card title="Objetos" href="/docs/red/v1/modelo/objetos">
  Modelo de identidad, canal y ciclo de vida de los objetos.
</Card>
