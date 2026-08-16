# Testing (/docs/red/v1/ingenieria/testing)



Un juego de red debe probarse como sistema distribuido, no sólo como código local.

## Matriz mínima [#matriz-mínima]

| Prueba              | Objetivo                 |
| ------------------- | ------------------------ |
| 1 cliente           | Gameplay base            |
| 2 clientes          | Replicación y ownership  |
| 4 clientes          | Escala prevista          |
| Cliente tardío      | Reconstrucción de estado |
| Desconexión         | Limpieza y ownership     |
| Reconexión          | Restauración             |
| Latencia            | Suavizado                |
| Pérdida             | Robustez                 |
| Spam de RFC         | Seguridad / carga        |
| Servidor reiniciado | Recuperación             |

## Orden [#orden]

Empieza por dos clientes. Un problema que sólo aparece con cuatro suele ser más difícil de aislar si la base no está validada.

## Invariantes [#invariantes]

Define reglas que nunca pueden romperse:

```text
Oro >= 0
Vida >= 0
Una torre tiene un único propietario lógico
Una unidad muerta no puede atacar
Un jugador desconectado no puede ejecutar acciones
```

Los tests deben intentar romper esas invariantes desde clientes distintos.

## Latencia y pérdida [#latencia-y-pérdida]

Prueba artificialmente condiciones peores que las esperadas. El objetivo es comprobar que la partida sigue siendo coherente, no que la animación sea idéntica en todos los clientes.

<Callout title="Prueba importante" type="info">
  Conecta un cliente después de que la partida ya tenga torres, unidades, recursos y oleadas activas. Si no puede reconstruir el estado correcto, la arquitectura depende demasiado de eventos históricos.
</Callout>
