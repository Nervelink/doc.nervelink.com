# Diferencias de Eco (/docs/red/v1/referencia/diferencias)



# Diferencias de Eco [#diferencias-de-eco]

Eco comienza como una adaptación de TNet 3, pero su objetivo es que la infraestructura pueda evolucionar dentro de la arquitectura de Nervelink sin depender de la nomenclatura histórica de TNet.

## Renombrado de la API [#renombrado-de-la-api]

La primera capa de cambio es la nomenclatura:

```text
TNManager      → Eco
TNObject       → Objeto
TNBehaviour    → Componente
TNChannel      → Canal
Player         → Jugador
Packet         → Paquete
DataNode       → Nodo
```

Estos cambios no pretenden modificar por sí mismos la semántica de cada sistema. Su objetivo inicial es adaptar la API al vocabulario de Pandora.

## Refactorizaciones [#refactorizaciones]

Además del renombrado, Eco introduce tipos y organización propios. Por eso no debe tratarse como una simple sustitución textual de nombres.

Algunos ejemplos son la organización bajo `Pandora`, las abstracciones `Lista<T>`, `Nodo`, `Entidad` y la integración con los sistemas propios del proyecto.

## Evolución independiente [#evolución-independiente]

TNet continúa evolucionando upstream. Eco puede incorporar cambios seleccionados, refactorizarlos o mantener decisiones propias aunque la implementación de TNet cambie posteriormente.

Esto hace necesario documentar siempre el comportamiento real de Eco y utilizar TNet como referencia secundaria.

## Regla documental [#regla-documental]

Cuando exista una diferencia entre un ejemplo de TNet y Eco, la documentación de Eco debe mostrar primero la solución de Eco y, cuando resulte útil, explicar la equivalencia o diferencia con TNet en una sección de referencia.

```text
TNet upstream
      │
      ├── concepto aprovechado
      ├── cambio
      └── funcionalidad no adoptada
             │
             ▼
            Eco
```

La comparación detallada debe mantenerse aquí o en la página específica del sistema afectado, no dentro de cada guía de uso.
