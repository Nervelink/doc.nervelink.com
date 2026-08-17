# RFC → RMR (/docs/red/v1/comunicacion/rfc)



# RFC → RMR [#rfc--rmr]

Esta página conserva la ruta histórica `rfc` para no romper enlaces antiguos, pero la terminología normativa de Eco es **RMR — Red Método Remoto**.

```text
TNet
RFC — Remote Function Call
        ↓
Eco
RMR — Red Método Remoto
```

<Callout title="Usa RMR en Eco" type="info">
  Cuando escribas código, documentación o arquitectura para Eco, utiliza `RMR`. La palabra `RFC` sólo debe aparecer al hablar de TNet, migración o compatibilidad histórica.
</Callout>

<Card title="Ir a RMR" href="/docs/red/v1/comunicacion/rmr">
  Consulta la documentación normativa de Red Método Remoto.
</Card>

## Tabla de equivalencias completa [#tabla-de-equivalencias-completa]

| TNet  | Eco   | Significado         |
| ----- | ----- | ------------------- |
| `RCC` | `RCR` | Red Creación Remota |
| `RFC` | `RMR` | Red Método Remoto   |
| `LCR` | `RCL` | Red Creación Local  |

## Por qué no se cambia la URL [#por-qué-no-se-cambia-la-url]

La ruta `/comunicacion/rfc` puede estar enlazada desde documentación antigua o proyectos que ya utilicen la documentación. Mantener esta página como puente evita enlaces rotos mientras la terminología visible de Eco utiliza `RMR`.
