# Migración de TNet a Eco (/docs/red/v1/ingenieria/migracion-tnet-eco)



Eco conserva la arquitectura conceptual de TNet, pero utiliza una terminología propia y puede incorporar cambios de comportamiento respecto al material histórico.

## Equivalencias principales [#equivalencias-principales]

| TNet                      | Eco        | Concepto                            |
| ------------------------- | ---------- | ----------------------------------- |
| TNManager                 | Eco        | Gestor global de red                |
| TNObject                  | Objeto     | Entidad de red                      |
| TNComponent / TNBehaviour | Componente | Comportamiento asociado a un objeto |
| Channel                   | Canal      | Ámbito de red                       |
| Packet                    | Paquete    | Unidad de protocolo                 |
| Player                    | Jugador    | Participante de la sesión           |
| Target                    | Objetivo   | Destinatarios de una operación      |
| Buffer                    | Buffer     | Lectura y escritura binaria         |
| Server                    | Servidor   | Autoridad/endpoint servidor         |
| Client                    | Cliente    | Endpoint cliente                    |

## Operaciones remotas [#operaciones-remotas]

Esta parte es especialmente importante porque Eco cambió también las siglas:

| TNet  | Eco   | Nombre en Eco       |
| ----- | ----- | ------------------- |
| `RCC` | `RCR` | Red Creación Remota |
| `RFC` | `RMR` | Red Método Remoto   |
| `LCR` | `RCL` | Red Creación Local  |

```text
TNet                         Eco
──────────────────────────────────────────
RCC  Remote Creation Call → RCR  Red Creación Remota
RFC  Remote Function Call → RMR  Red Método Remoto
LCR  Local Creation       → RCL  Red Creación Local
     Request / Call
```

<Callout title="Terminología normativa" type="info">
  En documentación y código de Eco utiliza `RCR`, `RMR` y `RCL`. Las siglas `RCC`, `RFC` y `LCR` se conservan únicamente para explicar TNet y realizar migraciones.
</Callout>

## No es sólo un reemplazo de nombres [#no-es-sólo-un-reemplazo-de-nombres]

Los ejemplos históricos de TNet pueden describir un workflow que ya no representa todas las capacidades disponibles en Eco. La documentación de Eco debe considerarse la referencia de comportamiento de Eco.

## Cómo migrar [#cómo-migrar]

1. Traduce primero los conceptos, no las llamadas literalmente.
2. Cambia `TNObject` por `Objeto` y `TNComponent`/`TNBehaviour` por `Componente`.
3. Revisa las operaciones remotas: `RCC → RCR`, `RFC → RMR`, `LCR → RCL`.
4. Comprueba ownership y canales.
5. Revisa si existe una funcionalidad moderna equivalente.
6. Revisa persistencia y destinatarios.
7. Valida el comportamiento contra el código de Eco.

<Callout title="Upstream" type="info">
  TNet sigue siendo una referencia útil para comprender el origen de muchas decisiones arquitectónicas. No debe tratarse automáticamente como documentación normativa de Eco.
</Callout>
