# Sincronización (/docs/red/v1/sincronizacion)



# Sincronización [#sincronización]

La sincronización tiene un objetivo diferente al de una llamada remota: no pretende simplemente comunicar que ocurrió una acción, sino mantener un estado coherente entre las instancias de un objeto.

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

El objeto expone `Get` y `Set` para trabajar con esos datos. El `Set` actualiza primero el estado local y, cuando procede, solicita su sincronización a través de la red. fileciteturn54file0

## `Get` y `Set` [#get-y-set]

El acceso básico es:

```csharp
var vida = objeto.Get<int>("vida");
objeto.Set("vida", 75);
```

También pueden utilizarse rutas jerárquicas:

```csharp
objeto.Set("estadisticas/vida", 75);
var vida = objeto.Get<int>("estadisticas/vida");
```

El almacenamiento jerárquico pertenece a `Nodo`; el objeto de red aporta el contexto y la sincronización.

## Propietario y autoridad [#propietario-y-autoridad]

Eco intenta que los cambios de datos pasen por el propietario del objeto. `Objeto.Set` actualiza el valor local y, cuando puede enviar, distingue entre el propietario y un participante que no es propietario. El propietario puede distribuir el cambio y actualizar el estado persistente del objeto; un cliente que no es propietario solicita el cambio al propietario. fileciteturn54file0

```text
                    Objeto
                       │
                ¿Quién es propietario?
                  /             \
                sí               no
                │                 │
         distribuye cambio   solicita al propietario
```

Esto evita que cada cliente se convierta en una fuente independiente de verdad para el mismo dato.

## Cambios cuando todavía no se puede enviar [#cambios-cuando-todavía-no-se-puede-enviar]

Un objeto puede encontrarse temporalmente sin capacidad para enviar: por ejemplo, durante la conexión o mientras se está entrando en un canal. En ese caso, `Objeto.Set` puede mantener cambios pendientes en una cola y enviarlos cuando `PuedeEnviar` vuelva a ser verdadero. fileciteturn54file0

El patrón es:

```text
Set()
  ↓
actualización local
  ↓
¿Puede enviar?
  ├── sí → sincronizar
  └── no → encolar
              ↓
         esperar disponibilidad
              ↓
          sincronizar
```

Esto es especialmente relevante durante cambios de escena o entrada a canales.

## Persistencia del estado [#persistencia-del-estado]

El sistema puede actualizar el estado persistente asociado al objeto. En el caso del propietario, `Set` no sólo informa a los demás participantes, sino que también envía el árbol completo para que el servidor pueda conservar el estado para futuros participantes. fileciteturn54file0

Por eso debemos distinguir:

```text
Cambio temporal
    ↓
informar a otros clientes

Cambio persistente
    ↓
informar a otros clientes
    +
actualizar estado almacenado
```

## `AutoSincronizar` [#autosincronizar]

Eco incluye `AutoSincronizar` como componente de conveniencia para sincronizar automáticamente campos o propiedades de Unity. El propio código deja claro que está pensado principalmente para prototipos rápidos y que los sistemas personalizados pueden ofrecer mejor rendimiento. fileciteturn66file0

```text
AutoSincronizar
├── entries
├── updatesPerSecond
├── isSavedOnServer
├── isImportant
└── onlyOwnerCanSync
```

Cada entrada referencia un `Component` y el nombre de un campo o propiedad. Durante la inicialización se resuelve mediante reflexión y se conserva su último valor conocido. fileciteturn66file0

## Frecuencia de actualización [#frecuencia-de-actualización]

`updatesPerSecond` limita la frecuencia máxima de comprobación y envío. Si el valor no cambia, no se genera una actualización nueva.

Por ejemplo:

```text
updatesPerSecond = 10

máximo: 10 comprobaciones por segundo
cambios: sólo se envía si el estado cambia
```

Si se establece en `0`, `AutoSincronizar` no realiza sincronización periódica; su comportamiento sigue orientado a proporcionar el estado a nuevos jugadores. fileciteturn66file0

## Transporte de la sincronización automática [#transporte-de-la-sincronización-automática]

`AutoSincronizar` permite decidir si el mensaje es importante. Cuando es importante utiliza el envío normal; cuando no lo es puede utilizar `SendQuickly`, dejando que Eco emplee el camino rápido apropiado. fileciteturn66file0

```text
Estado cambia
    ↓
AutoSincronizar.Sync()
    ├── importante → Send()
    └── no importante → SendQuickly()
```

La elección debe depender del significado del dato: una transformación que se actualiza constantemente puede tolerar pérdida intermedia; un estado crítico puede requerir transporte fiable.

## Propietario de la sincronización automática [#propietario-de-la-sincronización-automática]

Por defecto `onlyOwnerCanSync` está activado. Eso significa que el componente comprueba que `ero.isMine` sea verdadero antes de producir actualizaciones periódicas. fileciteturn66file0

Esto encaja con el modelo de autoridad de `Objeto`: un único participante produce el estado y los demás lo consumen.

## Estado inicial para nuevos jugadores [#estado-inicial-para-nuevos-jugadores]

Cuando `isSavedOnServer` está activado, el estado sincronizado puede formar parte del estado conservado en servidor y volver a estar disponible para participantes posteriores.

Cuando está desactivado, `AutoSincronizar` se suscribe al evento de entrada de jugadores y envía explícitamente los valores al nuevo participante. fileciteturn66file0

```text
isSavedOnServer = true
    ↓
servidor conserva estado
    ↓
nuevo jugador recibe estado

isSavedOnServer = false
    ↓
propietario detecta nuevo jugador
    ↓
envía estado actual directamente
```

## `Componente` como acceso de conveniencia [#componente-como-acceso-de-conveniencia]

`Componente` expone `Get` y `Set` delegando en su `Objeto` asociado y tiene `immediateSync` para controlar si un `Set` realizado desde el componente debe solicitar sincronización inmediata. fileciteturn60file0

```csharp
public class MiUnidad : Componente
{
    public void CambiarVida(int valor)
    {
        Set("vida", valor);
    }
}
```

El componente, por tanto, puede encapsular la lógica de gameplay sin gestionar directamente paquetes o buffers.

## Sincronización frente a RFC [#sincronización-frente-a-rfc]

Una regla práctica:

| Necesidad                                       | Mecanismo preferente                         |
| ----------------------------------------------- | -------------------------------------------- |
| Ejecutar una acción                             | RFC                                          |
| Comunicar una orden puntual                     | RFC                                          |
| Mantener una propiedad actualizada              | `Set` / estado                               |
| Sincronizar valores periódicos de un componente | sincronización específica                    |
| Prototipar rápidamente campos de Unity          | `AutoSincronizar`                            |
| Reconstruir estado al entrar un jugador         | datos persistentes / sincronización guardada |

No es recomendable convertir cada cambio de estado en una RFC de tipo `CambiarVida(75)`, `CambiarVida(74)`, `CambiarVida(73)` si lo que realmente existe es una propiedad `vida`.

## Recomendación para producción [#recomendación-para-producción]

`AutoSincronizar` es útil para validar rápidamente una idea, pero su implementación usa reflexión, comprueba valores periódicamente y empaqueta los valores seleccionados como un grupo. El código del propio componente recomienda crear sistemas personalizados y específicos para obtener mejor rendimiento. fileciteturn66file0

Para un sistema de producción conviene definir explícitamente:

```text
Estado
├── qué propiedades existen
├── quién tiene autoridad
├── cuándo cambian
├── con qué frecuencia se transmiten
├── si deben persistir
└── qué pérdida de paquetes es aceptable
```

## Referencia a TNet [#referencia-a-tnet]

`AutoSincronizar` deriva conceptualmente de `TNAutoSync`, mientras que `Objeto.Set` y `Objeto.Get` corresponden al modelo de datos asociado al antiguo `TNObject`.

Estas equivalencias sirven para migrar conocimientos, pero la fuente de verdad sigue siendo el comportamiento de `eco`.

<Card title="Eco" href="https://github.com/Nervelink/eco">
  Código fuente de la implementación actual.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream del que procede la arquitectura heredada.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Referencia externa para contrastar el diseño y la evolución de TNet.
</Card>
