# Sincronización (/docs/red/v1/comunicacion/sincronizacion)



# Sincronización [#sincronización]

La sincronización tiene un objetivo diferente al de una llamada remota: mantener un estado coherente entre las instancias de un objeto.

<Callout title="Idea principal" type="info">
  Una RFC comunica comportamiento. La sincronización comunica estado. Cuando un valor representa el estado actual de una entidad, normalmente debe pensarse primero como dato sincronizable y no como una sucesión de órdenes remotas.
</Callout>

## Estado de un objeto [#estado-de-un-objeto]

`Objeto` mantiene un `Nodo` asociado que puede utilizarse para almacenar datos de red.

```text
Objeto
 └── Nodo
      ├── propiedad A
      ├── propiedad B
      └── propiedad C
```

El objeto expone `Get` y `Set` para trabajar con esos datos.

## Propietario y autoridad [#propietario-y-autoridad]

Eco intenta que los cambios de datos pasen por el propietario del objeto. El propietario puede distribuir el cambio y actualizar el estado persistente; un cliente que no es propietario puede solicitar el cambio al propietario.

```text
Objeto
   │
   └── ¿Quién es propietario?
          ├── sí → distribuye
          └── no → solicita al propietario
```

Esto evita que cada cliente se convierta en una fuente independiente de verdad para el mismo dato.

## Cambios cuando todavía no se puede enviar [#cambios-cuando-todavía-no-se-puede-enviar]

Un objeto puede encontrarse temporalmente sin capacidad para enviar, por ejemplo durante la conexión o mientras se entra en un canal. En ese caso los cambios pueden quedar pendientes hasta que el objeto vuelva a estar preparado.

```text
Set()
  ↓
actualización local
  ↓
¿Puede enviar?
  ├── sí → sincronizar
  └── no → encolar
```

## `AutoSincronizar` [#autosincronizar]

Eco incluye `AutoSincronizar` como componente de conveniencia para sincronizar automáticamente campos o propiedades de Unity. Está pensado principalmente para prototipos rápidos; para producción pueden ser preferibles sistemas específicos y más eficientes.

```text
AutoSincronizar
├── entries
├── updatesPerSecond
├── isSavedOnServer
├── isImportant
└── onlyOwnerCanSync
```

Cada entrada referencia un `Component` y el nombre de un campo o propiedad. Durante la inicialización se resuelve mediante reflexión.

## Frecuencia de actualización [#frecuencia-de-actualización]

`updatesPerSecond` limita la frecuencia máxima de actualización. Si los valores no cambian, no se genera una transmisión nueva.

Con `0`, la sincronización periódica se desactiva.

## Transporte [#transporte]

`isImportant` permite distinguir entre actualizaciones que requieren el camino de envío normal y actualizaciones frecuentes en las que puede utilizarse `SendQuickly`.

La elección depende de la semántica del dato: una transformación de alta frecuencia puede tolerar perder valores intermedios; un estado crítico puede requerir transporte fiable.

## Estado para nuevos jugadores [#estado-para-nuevos-jugadores]

Cuando el estado se guarda en servidor, puede formar parte del estado que recibe un participante posterior.

Cuando `isSavedOnServer` está desactivado, `AutoSincronizar` puede enviar el estado actual al jugador que acaba de entrar.

## Sincronización frente a RFC [#sincronización-frente-a-rfc]

| Necesidad                          | Mecanismo                 |
| ---------------------------------- | ------------------------- |
| Ejecutar una acción                | RFC                       |
| Comunicar una orden puntual        | RFC                       |
| Mantener una propiedad actualizada | `Set` / estado            |
| Sincronizar valores periódicos     | Sincronización específica |
| Prototipar campos de Unity         | `AutoSincronizar`         |

No es recomendable convertir cada cambio de estado en una RFC si lo que realmente existe es una propiedad persistente.

## Producción [#producción]

`AutoSincronizar` utiliza reflexión y comprobación periódica de propiedades. Para sistemas de producción conviene definir explícitamente qué estado existe, quién tiene autoridad, cuándo cambia, con qué frecuencia se transmite, si debe persistir y qué pérdida de paquetes es aceptable.

## Relación con TNet [#relación-con-tnet]

`AutoSincronizar` deriva conceptualmente de `TNAutoSync`, mientras que `Objeto.Set` y `Objeto.Get` corresponden al modelo de datos asociado al antiguo `TNObject`.

La fuente de verdad para Eco sigue siendo `Nervelink/eco`.

## Referencias [#referencias]

<Card title="Eco" href="https://github.com/Nervelink/eco">
  Código fuente de la implementación actual.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Referencia generada sobre el TNet actual.
</Card>
