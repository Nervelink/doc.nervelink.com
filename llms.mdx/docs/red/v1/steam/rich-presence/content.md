# Rich Presence (/docs/red/v1/steam/rich-presence)



La integración de Eco utiliza la clave `connect` de Steam Rich Presence como puente para la función de unirse a una partida.

No debe confundirse Rich Presence con un sistema de lobby de Eco. Es una capa de descubrimiento y experiencia de usuario.

## Estado [#estado]

```text
Eco desconectado
    ↓
connect = ""

Servidor listo
    ↓
connect = "+connect <identificador>"
```

`Steam.PermitirUnirseAmigos` actualiza ese valor según el estado de la conexión. fileciteturn190file0

## Amigos con partida [#amigos-con-partida]

La integración dispone de `ObtenerAmigosConPartida()`, que recorre los amigos inmediatos y busca el campo `connect` para identificar aquellos que anuncian una partida. fileciteturn190file0

Esto permite construir una UI propia de "amigos jugando" sin hacer que Steam determine la estructura del lobby Eco.

## Seguridad [#seguridad]

El valor `connect` debe tratarse como una pista de descubrimiento. La entrada real debe validarse en Eco.
