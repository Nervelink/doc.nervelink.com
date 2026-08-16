# Autoridad y ownership (/docs/red/v1/ingenieria/autoridad-y-ownership)



Estos conceptos suelen mezclarse y generan arquitecturas frágiles.

| Concepto              | Significado                                                 |
| --------------------- | ----------------------------------------------------------- |
| Autoridad             | Quién puede determinar el estado válido                     |
| Ownership de red      | Qué participante controla ciertas operaciones de un objeto  |
| Propiedad de gameplay | A quién pertenece la entidad dentro de las reglas del juego |

No tienen que ser idénticos.

## Ejemplo [#ejemplo]

```text
Jugador 2
   │
   ├── propietario de gameplay de Torre 15
   └── owner de Eco de Torre 15
```

Si el jugador abandona, la torre puede seguir perteneciendo al jugador en el modelo de partida mientras el servidor toma control de las operaciones de red.

## Regla de autoridad [#regla-de-autoridad]

Cualquier dato que pueda alterar el resultado de la partida debe poder validarse en la autoridad del juego.

Esto incluye recursos, daño, muerte, construcción, mejoras, objetivos y victoria.

## Transferencia [#transferencia]

Antes de transferir ownership define:

1. quién puede solicitarla;
2. quién la aprueba;
3. qué datos siguen siendo válidos;
4. qué ocurre con acciones pendientes;
5. qué sucede si el receptor se desconecta.

<Callout title="Error habitual" type="error">
  Que un objeto sea `isMine` no significa que el cliente deba poder cambiar cualquier aspecto del gameplay. El servidor puede seguir siendo la autoridad final.
</Callout>
