# Sincronización avanzada (/docs/red/v1/comunicacion/sincronizacion-avanzada)



# Sincronización avanzada [#sincronización-avanzada]

El sistema de datos de `Objeto` actualiza localmente el valor y, cuando puede enviar, comunica el cambio por la red. Si el objeto todavía no puede enviar, Eco puede poner el cambio en una cola para transmitirlo posteriormente. fileciteturn235file0

## Flujo [#flujo]

```text
Set(...)
  ↓
Actualizar estado local
  ↓
¿Puede enviar?
 ├─ No → cola pendiente
 └─ Sí → enviar cambio
          ↓
       servidor / propietario
          ↓
      resto de clientes
```

## Autoridad [#autoridad]

Idealmente el propietario del objeto realiza el cambio. Si otro participante intenta modificarlo, Eco puede reenviar la petición al propietario para mantener una autoridad clara. fileciteturn235file0

Esto evita que dos clientes escriban simultáneamente el mismo estado sin una regla de autoridad.

## Estado local inmediato [#estado-local-inmediato]

`Set` actualiza la copia local antes de terminar el viaje de red. Esto mejora la respuesta inmediata, pero implica que la interfaz puede observar un valor antes de que el servidor haya confirmado la operación.

Por eso los sistemas críticos no deberían confundir “valor local solicitado” con “estado autoritativo confirmado”.

## Datos completos frente a cambios incrementales [#datos-completos-frente-a-cambios-incrementales]

Durante la creación puede ser razonable enviar un `Nodo` completo. Durante el juego resulta más eficiente enviar cambios concretos:

```text
Creación → estado inicial completo
Runtime  → Set("vida", 75)
Runtime  → Set("mana", 40)
```

El propio código recomienda utilizar `Get/Set` después de la creación en lugar de reemplazar continuamente todo el árbol de datos. fileciteturn235file0

## AutoSincronizar [#autosincronizar]

`AutoSincronizar` puede automatizar la comparación y envío de valores, pero debe utilizarse con criterio: comprobar campos mediante reflexión periódicamente tiene un coste y puede ocultar la autoridad real del estado.

## Referencias [#referencias]

<Card title="Sincronización" href="/docs/red/v1/comunicacion/sincronizacion">
  Modelo básico de sincronización de Eco.
</Card>

<Card title="Sincronizar una entidad" href="/docs/red/v1/guias/sincronizar-entidad">
  Workflow práctico para sincronizar un objeto.
</Card>
