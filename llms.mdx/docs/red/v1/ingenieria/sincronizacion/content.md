# Sincronización (/docs/red/v1/ingenieria/sincronizacion)



No todo dato que cambia debe enviarse por red. La primera decisión es clasificarlo.

| Tipo        | Ejemplo         | Tratamiento               |
| ----------- | --------------- | ------------------------- |
| Intención   | Construir torre | RFC / solicitud           |
| Estado      | Vida actual     | Sincronización            |
| Evento      | Torre destruida | Evento o cambio de estado |
| Persistente | Torre guardada  | Persistencia              |
| Visual      | Partículas      | Local                     |
| Derivado    | Barra de vida   | Reconstrucción local      |

## Frecuencia [#frecuencia]

Una entidad no necesita una frecuencia universal. Una unidad en combate puede requerir actualizaciones frecuentes; una torre inactiva puede necesitar muy pocas.

## Tamaño [#tamaño]

Sincroniza identificadores y valores mínimos. Si todos los clientes ya tienen `UnidadAsset`, no envíes nombre, icono, estadísticas estáticas ni prefab.

## Estado frente a evento [#estado-frente-a-evento]

Un evento responde a "qué ocurrió". El estado responde a "qué es cierto ahora".

Si un cliente puede conectarse tarde y necesita conocer el resultado, ese resultado debe existir como estado reconstruible, no sólo como evento histórico.

## Destinatarios [#destinatarios]

Cada actualización debe responder:

```text
¿Quién necesita conocerla?
¿Durante cuánto tiempo?
¿Es persistente?
¿Puede reconstruirse?
```

Usa `Objetivo` y el ámbito de `Canal` para expresar el destino. No crees canales nuevos para cada excepción de visibilidad.

## Interpolación [#interpolación]

El estado recibido es una referencia lógica. La representación visual puede interpolarse para ocultar la naturaleza discreta de los paquetes.

<Callout title="No sincronices Transform por reflejo" type="warn">
  Primero determina qué estado necesita realmente otro cliente. El Transform suele ser una representación de ese estado, no necesariamente el estado que debería viajar directamente.
</Callout>
