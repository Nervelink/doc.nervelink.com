# AnimacionPersonaje (/docs/personaje/v1/referencia/animacion-personaje)



`AnimacionPersonaje` mantiene la presentación del personaje sincronizada con su estado de movimiento.

## Responsabilidades [#responsabilidades]

* conservar la referencia del `Animator`;
* asignar variables críticas del controlador;
* actualizar parámetros de animación;
* activar y desactivar la presentación cuando el objeto cambia de estado.

## Integración [#integración]

`Jugador` y `Personaje` pueden obtener la instancia mediante `ObtenerAnimPersonaje()`.
