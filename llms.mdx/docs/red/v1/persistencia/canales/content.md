# Canales persistentes (/docs/red/v1/persistencia/canales)



# Canales persistentes [#canales-persistentes]

Un canal puede marcarse como persistente para permanecer abierto aunque temporalmente no tenga jugadores. El protocolo de entrada incluye esta propiedad explícitamente. fileciteturn227file0

## Canal normal [#canal-normal]

```text
Último jugador sale
       ↓
Canal puede cerrarse
       ↓
Estado liberado
```

## Canal persistente [#canal-persistente]

```text
Último jugador sale
       ↓
Canal permanece abierto
       ↓
Estado conservado
       ↓
Otro jugador entra posteriormente
```

Esto resulta útil para mundos persistentes, lobbies estables o espacios que no deben desaparecer simplemente porque estén vacíos.

## Persistente no significa inmortal [#persistente-no-significa-inmortal]

El servidor sigue pudiendo cerrar explícitamente el canal. `RequestCloseChannel` está definido como una operación que marca el canal para cierre y elimina sus datos guardados. fileciteturn227file0

## Memoria [#memoria]

La persistencia y la memoria activa son conceptos distintos. `ServidorJuego.Sleep()` puede descargar de memoria canales sin jugadores y `Wake()` volver a activarlos. fileciteturn233file0

## Elegir persistencia [#elegir-persistencia]

| Caso                                    | Persistente    |
| --------------------------------------- | -------------- |
| Partida temporal de cuatro jugadores    | Normalmente no |
| Mundo persistente                       | Sí             |
| Evento que debe desaparecer al terminar | No             |
| Lobby estable                           | Puede serlo    |

## Referencias [#referencias]

<Card title="Varios canales simultáneos" href="/docs/red/v1/guias/multiples-canales">
  Uso de varios canales desde un único cliente.
</Card>
