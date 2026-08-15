# Ciclo de vida (/docs/red/v1/fundamentos/ciclo-de-vida)



# Ciclo de vida [#ciclo-de-vida]

Una parte importante de la arquitectura de Eco ocurre fuera de las llamadas que haces desde gameplay. El sistema mantiene instancias de cliente, servidor, actualizadores y colas para evitar que cada componente tenga que implementar su propio ciclo de red.

## Arranque del sistema [#arranque-del-sistema]

`Eco` se integra con el arranque de Pandora y se inicializa como sistema. Su `Iniciar()` registra sus RCR, instala los eventos internos y crea el servidor local cuando corresponde.

```text
Alexandria
    │
    └── Eco.Iniciar()
          ├── Registrar RCR
          ├── Inicializar eventos
          ├── Crear / iniciar Servidor
          └── Activar cliente y procesamiento
```

No necesitas llamar manualmente a `Eco.Iniciar()` en el uso normal.

## Componente y ActualizadorRed [#componente-y-actualizadorred]

`Componente` no depende exclusivamente de los callbacks estándar de `MonoBehaviour` para integrar el objeto de red. En `Awake()` obtiene o prepara el `Objeto` asociado y registra el componente en `ActualizadorRed`.

Esto permite centralizar varias operaciones que de otro modo acabarían repartidas entre cientos de `Update`, `LateUpdate` y `Start`.

```text
Componente
    │
    ├── Awake()
    │     ├── localizar Objeto
    │     └── ActualizadorRed.AddStart(this)
    │
    └── OnStart()
          └── asegurar Objeto y métodos remotos
```

`ActualizadorRed` también admite `IUpdateable`, `ILateUpdateable`, `IInfrequentUpdateable`, callbacks de un solo uso, invocaciones retardadas y coroutines.

## Procesamiento de paquetes [#procesamiento-de-paquetes]

El cliente recibe bytes del transporte, los convierte en `Buffer` y procesa los paquetes dentro del ciclo de actualización de Eco.

```text
TCP / UDP
   ↓
Buffer recibido
   ↓
Paquete
   ↓
ClienteJuego
   ↓
Despacho
   ├── evento de conexión/canal/jugador
   ├── creación / destrucción de Objeto
   ├── cambio de propietario
   ├── datos
   └── RFC
```

El método `Eco.ProcesarPaquetes` es reemplazable para integraciones avanzadas. En la configuración normal procesa la cola del `ClienteJuego` una vez que las condiciones de carga de escena lo permiten.

## Carga de escenas [#carga-de-escenas]

La carga de escena está coordinada con el canal. Cuando el servidor solicita un cambio de nivel, Eco registra el canal como “en carga”, limpia los objetos de ese canal y espera a que el `AsyncOperation` llegue al punto seguro antes de activar la escena.

Esto explica por qué crear objetos inmediatamente durante un cambio de escena puede ser rechazado: el objetivo es evitar que un objeto se cree en una representación del canal que todavía está cambiando.

## Apagado [#apagado]

El apagado sigue el camino inverso:

```text
Application Quit / Alexandria
        ↓
Eco.Apagar()
        ↓
Desconectar cliente
        ↓
Detener UDP
        ↓
Apagar Servidor
        ↓
Eliminar referencias globales
```

Eco marca internamente el estado de apagado para impedir nuevas operaciones de red mientras Unity está destruyendo objetos.

<Callout title="No fuerces el ciclo de vida" type="warn">
  Crear manualmente otro `Eco`, llamar `Iniciar()` varias veces o mantener referencias a objetos de red durante el apagado puede romper las garantías de singleton y de limpieza.
</Callout>

## Qué aporta esta arquitectura [#qué-aporta-esta-arquitectura]

La idea procede del patrón de TNet de centralizar la actualización y el procesamiento, pero en Eco el vocabulario y los puntos de entrada son propios. DeepWiki identifica este patrón como parte de la arquitectura de cliente de TNet; en Eco se refleja en `Eco`, `ClienteJuego`, `Objeto`, `Componente` y `ActualizadorRed`.

<Card title="ActualizadorRed" href="/docs/red/v1/avanzado/actualizador-red">
  Referencia detallada del scheduler de actualización.
</Card>

<Card title="Arquitectura" href="/docs/red/v1/fundamentos/arquitectura">
  Visión general de todas las capas.
</Card>
