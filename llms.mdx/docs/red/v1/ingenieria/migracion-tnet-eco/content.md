# Migración de TNet a Eco (/docs/red/v1/ingenieria/migracion-tnet-eco)



Eco conserva la arquitectura conceptual de TNet, pero utiliza una terminología propia y puede incorporar cambios de comportamiento respecto al material histórico.

| TNet        | Eco        |
| ----------- | ---------- |
| TNManager   | Eco        |
| TNObject    | Objeto     |
| TNComponent | Componente |
| Channel     | Canal      |
| Packet      | Paquete    |
| Player      | Jugador    |
| Target      | Objetivo   |
| Buffer      | Buffer     |
| Server      | Servidor   |
| Client      | Cliente    |

## No es sólo un reemplazo de nombres [#no-es-sólo-un-reemplazo-de-nombres]

Los ejemplos históricos de TNet pueden describir un workflow que ya no representa todas las capacidades disponibles en Eco. La documentación de Eco debe considerarse la referencia de comportamiento de Eco.

## Cómo migrar [#cómo-migrar]

1. Traduce primero los conceptos, no las llamadas literalmente.
2. Comprueba ownership y canales.
3. Revisa si existe una funcionalidad moderna equivalente.
4. Revisa persistencia y destinatarios.
5. Valida el comportamiento contra el código de Eco.

<Callout title="Upstream" type="info">
  TNet sigue siendo una referencia útil para comprender el origen de muchas decisiones arquitectónicas. No debe tratarse automáticamente como documentación normativa de Eco.
</Callout>
