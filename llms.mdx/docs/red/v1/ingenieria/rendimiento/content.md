# Rendimiento (/docs/red/v1/ingenieria/rendimiento)



El rendimiento de una partida conectada depende de cuatro variables principales:

```text
entidades × frecuencia × tamaño × destinatarios
```

Reducir cualquiera de ellas puede reducir el coste total.

## Ancho de banda [#ancho-de-banda]

No envíes datos que todos los clientes pueden reconstruir. Agrupa cambios cuando el modelo lo permita y evita mensajes por frame sin necesidad real.

## CPU [#cpu]

Serialización, reflexión, búsqueda de objetivos, pathfinding y procesamiento de callbacks pueden competir con el frame principal. Mide antes de optimizar.

## Memoria [#memoria]

Evita generar buffers, listas y objetos temporales constantemente. En partidas con muchas entidades, pequeñas asignaciones repetidas se convierten en presión de GC.

## Escalabilidad [#escalabilidad]

Un escenario con 4 jugadores y 500 enemigos no debe diseñarse como 4 jugadores y 10 enemigos.

```text
4 jugadores
× 500 enemigos
× frecuencia de actualización
× bytes por entidad
× destinatarios
```

Ese cálculo conceptual debe formar parte del diseño inicial.

## Perfilado [#perfilado]

Mide al menos:

* bytes enviados y recibidos;
* paquetes por segundo;
* tamaño medio y máximo de paquete;
* tiempo de serialización;
* tiempo de procesamiento de paquetes;
* número de objetos activos;
* número de RFC;
* allocations y GC;
* latencia observada.

<Callout title="Objetivo" type="info">
  El objetivo no es minimizar el número de paquetes a cualquier precio. Es transmitir el estado necesario con un coste predecible y una frecuencia adecuada.
</Callout>
