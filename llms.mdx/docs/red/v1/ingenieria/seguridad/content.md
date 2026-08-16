# Seguridad (/docs/red/v1/ingenieria/seguridad)



Eco transporta datos; no sustituye la validación de las reglas del juego.

## Cliente no confiable [#cliente-no-confiable]

Cualquier valor recibido debe considerarse potencialmente manipulado.

El servidor debe validar como mínimo:

* identidad del jugador;
* ownership;
* existencia del objeto;
* canal válido;
* permisos;
* costes;
* rangos;
* cooldowns;
* estado actual de la partida;
* límites de parámetros.

## Ejemplo [#ejemplo]

Una solicitud:

```text
Construir torre X en celda Y
```

no significa que el servidor deba ejecutar directamente `Construir(X, Y)`.

Debe convertirse en:

```text
¿Jugador válido?
¿Raza permite X?
¿Tiene recursos?
¿Celda Y es válida?
¿Puede construir ahora?
¿La partida lo permite?
```

Sólo entonces se modifica el estado.

## Rate limiting [#rate-limiting]

Las acciones que pueden ser abusadas deben tener límites razonables. Un cliente no debería poder provocar trabajo ilimitado enviando RFC a máxima velocidad.

## Datos del cliente [#datos-del-cliente]

El cliente puede enviar identificadores, posiciones solicitadas o parámetros de selección. No debe ser la fuente final de valores económicos o resultados de combate.

<Callout title="Seguridad de gameplay" type="error">
  Ocultar una opción en UI no es una medida de seguridad. Si una regla importa, debe comprobarse en la autoridad.
</Callout>
