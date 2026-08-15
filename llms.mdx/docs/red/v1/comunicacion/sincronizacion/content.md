# Sincronización (/docs/red/v1/comunicacion/sincronizacion)



# Sincronización [#sincronización]

La sincronización responde a una pregunta distinta de una RFC: &#x2A;*¿cómo hago que todos conozcan el estado actual de una entidad?**

<Callout title="Acción vs estado" type="info">
  Una RFC representa normalmente un evento puntual. Un dato sincronizado representa el estado actual y puede necesitar volver a enviarse o reconstruirse para un jugador que entra más tarde.
</Callout>

## Modelo de datos [#modelo-de-datos]

```text
Objeto
└── Nodo
    ├── vida
    ├── posición
    ├── inventario
    └── estado
```

Las operaciones principales son `Get` y `Set`.

```csharp
objeto.Set("vida", 100);
int vida = objeto.Get<int>("vida");
```

## Qué ocurre al llamar a `Set` [#qué-ocurre-al-llamar-a-set]

<div className="fd-steps">
  <div className="fd-step">
    ### Estado local \[step] [#1-estado-local-step]

    Eco actualiza primero el `Nodo` local. Esto permite que el código que acaba de cambiar el dato lo vea inmediatamente.
  </div>

  <div className="fd-step">
    ### Comprobación de capacidad \[step] [#2-comprobación-de-capacidad-step]

    Se comprueba que el objeto tenga identidad válida, pertenezca a un contexto enviable y no esté atravesando una transición donde la transmisión todavía no sea posible.
  </div>

  <div className="fd-step">
    ### Comprobación de autoridad \[step] [#3-comprobación-de-autoridad-step]

    Si el llamador es propietario, puede distribuir el cambio. Si no lo es, la operación se dirige al propietario para que siga el flujo de autoridad.
  </div>

  <div className="fd-step">
    ### Propagación \[step] [#4-propagación-step]

    El cambio se comunica a los destinatarios y el estado puede actualizarse también en la representación persistente del servidor.
  </div>

  <div className="fd-step">
    ### Cola temporal \[step] [#5-cola-temporal-step]

    Si el objeto no puede enviar todavía, el cambio puede quedar pendiente hasta que la transmisión vuelva a estar disponible.
  </div>
</div>

## Autoridad [#autoridad]

```text
Cliente A
   │
   │ Set("vida", 80)
   ▼
¿A es propietario?
   │
   ├── Sí ──► distribuir + actualizar estado persistente
   │
   └── No ─► solicitar al propietario
```

Esto evita que varios clientes actúen como fuente de verdad simultánea para una propiedad.

## Qué sincronizar [#qué-sincronizar]

<Tabs items="['Estado crítico', 'Alta frecuencia', 'Inicialización']">
  <Tab value="Estado crítico">
    Vida, inventario, reglas de partida y cualquier valor cuyo estado actual deba ser consistente. Prioriza claridad y autoridad sobre frecuencia extrema.
  </Tab>

  <Tab value="Alta frecuencia">
    Transformaciones o valores que cambian muchas veces por segundo pueden usar mecanismos rápidos si perder estados intermedios es aceptable.
  </Tab>

  <Tab value="Inicialización">
    Datos necesarios para reconstruir un objeto cuando aparece o cuando entra un jugador nuevo deben formar parte de un estado que pueda recuperarse.
  </Tab>
</Tabs>

## `AutoSincronizar` [#autosincronizar]

`AutoSincronizar` automatiza parte de este trabajo para campos y propiedades de componentes de Unity. Su configuración incluye conceptos como frecuencia, persistencia y autoridad del propietario.

```text
AutoSincronizar
├── entries
├── updatesPerSecond
├── isSavedOnServer
├── isImportant
└── onlyOwnerCanSync
```

<Callout title="Conveniencia, no arquitectura" type="idea">
  `AutoSincronizar` es útil para prototipos y propiedades sencillas. En sistemas de producción conviene controlar explícitamente qué datos viajan, quién los modifica y con qué frecuencia.
</Callout>

## Frecuencia [#frecuencia]

Si una propiedad cambia a alta frecuencia, transmitir todos sus valores puede generar tráfico innecesario.

```text
100 cambios locales / s
        │
        ├── enviar todos ───────► tráfico alto
        │
        └── muestrear / agrupar ─► tráfico controlado
```

`updatesPerSecond` establece una frecuencia máxima para el sistema automático.

## Persistencia [#persistencia]

La sincronización de estado y la persistencia no son lo mismo:

| Concepto       | Pregunta                                         |
| -------------- | ------------------------------------------------ |
| Sincronización | ¿Quién necesita conocer el estado ahora?         |
| Persistencia   | ¿Debe poder reconstruirse el estado después?     |
| RFC guardada   | ¿Debe reproducirse una operación al reconstruir? |

Consulta [Persistencia](/docs/red/v1/persistencia) cuando el objetivo sea sobrevivir a la ausencia de jugadores.

## Errores habituales [#errores-habituales]

<Callout title="No conviertas el estado en eventos" type="warn">
  Si una unidad tiene `vida = 74`, no necesitas una RFC por cada cambio si lo único importante es que los clientes conozcan el valor actual. Sincroniza el estado.
</Callout>

<Callout title="No sincronices cada frame por defecto" type="warn">
  La frecuencia debe depender de la percepción del jugador y de la necesidad real del sistema, no del `Update()` de Unity.
</Callout>

## Relación con TNet [#relación-con-tnet]

El modelo procede conceptualmente de `TNObject.Set/Get` y `TNAutoSync`, pero los nombres y el comportamiento deben comprobarse en la implementación de Eco.

## Referencias [#referencias]

<Cards>
  <Card title="Objetos" href="/docs/red/v1/modelo/objetos">
    Identidad y datos del objeto.
  </Card>

  <Card title="RFC avanzadas" href="/docs/red/v1/comunicacion/rfc-avanzadas">
    Cuándo una acción debe ser un evento remoto.
  </Card>

  <Card title="Sincronizar una entidad" href="/docs/red/v1/guias/sincronizar-entidad">
    Implementación práctica de este flujo.
  </Card>

  <Card title="Persistencia" href="/docs/red/v1/persistencia">
    Cómo hacer que el estado sobreviva a la ausencia temporal de jugadores.
  </Card>
</Cards>
