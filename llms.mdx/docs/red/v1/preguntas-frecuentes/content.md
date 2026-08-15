# Preguntas frecuentes (/docs/red/v1/preguntas-frecuentes)



# Preguntas frecuentes [#preguntas-frecuentes]

Esta página reúne dudas que suelen aparecer al integrar Eco o al trasladar conocimientos de TNet 3 a la arquitectura actual de Nervelink.

<Callout title="Fuente de verdad" type="warn">
  La documentación de TNet y DeepWiki es útil para entender el origen del sistema, pero el código de `Nervelink/eco` tiene prioridad cuando existe una diferencia de nombres, API o comportamiento.
</Callout>

## Arquitectura [#arquitectura]

### ¿Eco es una implementación nueva de networking? [#eco-es-una-implementación-nueva-de-networking]

No. Eco parte de TNet 3 y conserva gran parte de su arquitectura y comportamiento, pero utiliza la nomenclatura y organización de Nervelink y puede incorporar cambios propios.

### ¿Eco es simplemente un renombrado de TNet? [#eco-es-simplemente-un-renombrado-de-tnet]

No conviene tratarlo como un reemplazo mecánico de nombres. Aunque existen equivalencias directas como `TNManager` → `Eco`, `TNObject` → `Objeto` y `TNBehaviour` → `Componente`, la documentación debe seguir la implementación de Eco.

### ¿Qué repositorio debo consultar? [#qué-repositorio-debo-consultar]

Para usar Eco, consulta primero:

<Card title="Eco" href="https://github.com/Nervelink/eco">
  Implementación actual de Nervelink.
</Card>

Para comparar el origen de una característica:

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Referencia de arquitectura y navegación sobre el repositorio upstream.
</Card>

## Conexiones y canales [#conexiones-y-canales]

### ¿Un cliente sólo puede estar en un canal? [#un-cliente-sólo-puede-estar-en-un-canal]

No. Una conexión puede pertenecer simultáneamente a varios `Canal`. La pertenencia a canales es una capa lógica sobre la conexión de red.

```text
Cliente
└── Conexión
    ├── Canal A
    ├── Canal B
    └── Canal C
```

Consulta [Canales](/docs/red/v1/modelo/canales) para conocer cómo se gestionan entradas, salidas y estados múltiples.

### ¿Canal y conexión son lo mismo? [#canal-y-conexión-son-lo-mismo]

No.

| Concepto | Función                           |
| -------- | --------------------------------- |
| Conexión | Comunicación con el servidor.     |
| Canal    | Ámbito de estado y participantes. |
| Objeto   | Identidad de una entidad de red.  |
| Jugador  | Participante de la sesión.        |

### ¿Entrar en un canal significa que ya puedo enviar inmediatamente? [#entrar-en-un-canal-significa-que-ya-puedo-enviar-inmediatamente]

No necesariamente. Durante la unión existen estados transitorios. Debes esperar a que el canal haya quedado registrado en la sesión y a que el objeto pueda enviar. `Objeto.PuedeEnviar` existe precisamente para comprobar esta condición en operaciones asociadas a objetos.

### ¿Puedo abandonar un canal sin desconectarme? [#puedo-abandonar-un-canal-sin-desconectarme]

Sí. Salir de un `Canal` no termina la conexión con el servidor. La misma conexión puede continuar participando en otros canales.

## Objetos y componentes [#objetos-y-componentes]

### ¿Qué diferencia hay entre `Objeto` y `Componente`? [#qué-diferencia-hay-entre-objeto-y-componente]

`Objeto` proporciona la identidad de red, propiedad, canal, datos y ciclo de vida. `Componente` aporta comportamiento asociado a ese objeto.

```text
GameObject
├── Objeto
├── Componente
└── Componente
```

### ¿Qué diferencia hay entre `id` y `uid`? [#qué-diferencia-hay-entre-id-y-uid]

`id` identifica el objeto dentro de su contexto de canal. `uid` combina la identidad del objeto con el canal para permitir una identificación global del objeto de red.

### ¿Puedo cambiar el propietario de cualquier objeto? [#puedo-cambiar-el-propietario-de-cualquier-objeto]

No. Los objetos estáticos tienen restricciones distintas de los objetos dinámicos. En Eco, el cambio de propietario se valida además contra la pertenencia del jugador destino al canal del objeto.

### ¿Qué significa `isMine`? [#qué-significa-ismine]

Indica si el propietario del objeto es el jugador local. No significa que el objeto sea visible únicamente para ese jugador.

## Comunicación y sincronización [#comunicación-y-sincronización]

### ¿RFC o sincronización? [#rfc-o-sincronización]

Usa RFC para acciones o eventos puntuales y sincronización para mantener propiedades o estado.

```text
"Abre la puerta"      → RFC
"La puerta está abierta" → Estado
```

Consulta [Comunicación](/docs/red/v1/comunicacion) y [Sincronización](/docs/red/v1/comunicacion/sincronizacion).

### ¿Qué ocurre cuando llamo a `Objeto.Set` y todavía no puede enviar? [#qué-ocurre-cuando-llamo-a-objetoset-y-todavía-no-puede-enviar]

El valor se aplica localmente y el cambio puede quedar pendiente para transmitirse cuando el objeto vuelva a estar en condiciones de enviar. Esto es importante durante uniones, transiciones de canal y otros estados temporales.

### ¿Debo usar `AutoSincronizar` para todo? [#debo-usar-autosincronizar-para-todo]

No. Es una herramienta de conveniencia para casos sencillos y prototipos. Los sistemas críticos suelen beneficiarse de un estado explícito, autoridad clara y una frecuencia de actualización diseñada para cada dato.

### ¿La sincronización siempre pasa por el propietario? [#la-sincronización-siempre-pasa-por-el-propietario]

La implementación de `Objeto.Set` está diseñada para que las solicitudes de cambio pasen por el propietario cuando el emisor no es el propietario local. Esto ayuda a mantener una fuente de autoridad única para el estado del objeto.

## Transporte [#transporte]

### ¿TCP y UDP se pueden utilizar indistintamente? [#tcp-y-udp-se-pueden-utilizar-indistintamente]

No. TCP ofrece entrega fiable y ordenada. UDP está pensado para tráfico donde la pérdida o el procesamiento de valores intermedios puede ser aceptable.

### ¿Usar UDP hace automáticamente una operación más rápida? [#usar-udp-hace-automáticamente-una-operación-más-rápida]

No. El transporte afecta a latencia, fiabilidad y coste, pero la semántica de la operación sigue siendo responsabilidad del diseño de comunicación.

### ¿Qué es un `Buffer`? [#qué-es-un-buffer]

Es el contenedor binario que Eco utiliza para escribir y leer datos que forman parte de los mensajes de red. Se encuentra por debajo del nivel de gameplay y del protocolo.

## Servidor [#servidor]

### ¿Puedo ejecutar cliente y servidor dentro de la misma instancia de Unity? [#puedo-ejecutar-cliente-y-servidor-dentro-de-la-misma-instancia-de-unity]

Sí. Eco dispone de un modo de servidor local que conecta `ClienteJuego` y `ServidorJuego` dentro del mismo proceso. Es útil para pruebas, pero no sustituye a una prueba de red real entre procesos o máquinas.

### ¿El servidor siempre es multihilo? [#el-servidor-siempre-es-multihilo]

La implementación está preparada para ejecución multihilo y puede compilarse con `SINGLE_THREADED` cuando se necesita ejecutar el procesamiento sin el hilo de red dedicado.

### ¿Qué persiste realmente el servidor? [#qué-persiste-realmente-el-servidor]

Depende del mecanismo utilizado. Eco distingue entre datos del servidor, estado de canal, datos de jugadores, objetos persistentes y RFC guardadas. No todo dato que existe durante una sesión se convierte automáticamente en una partida persistida.

## Depuración [#depuración]

### ¿Dónde miro cuando un mensaje no llega? [#dónde-miro-cuando-un-mensaje-no-llega]

Sigue la cadena de abajo hacia arriba:

<Steps>
  <Step>
    ### Comprueba la operación [#comprueba-la-operación]

    Confirma que el juego realmente está llamando al método esperado.
  </Step>

  <Step>
    ### Comprueba el objeto y el canal [#comprueba-el-objeto-y-el-canal]

    Verifica `id`, `channelID`, `hasBeenRegistered` y `PuedeEnviar` cuando la operación depende de un `Objeto`.
  </Step>

  <Step>
    ### Comprueba el protocolo [#comprueba-el-protocolo]

    Determina qué `Paquete` está representando la operación.
  </Step>

  <Step>
    ### Comprueba transporte y servidor [#comprueba-transporte-y-servidor]

    Revisa TCP/UDP, recepción del servidor y el procesamiento del paquete.
  </Step>
</Steps>

### ¿Qué hago si una API de TNet no existe en Eco? [#qué-hago-si-una-api-de-tnet-no-existe-en-eco]

No la sustituyas automáticamente por una función con el mismo nombre aproximado. Busca primero la equivalencia en [Equivalencias Eco ↔ TNet](/docs/red/v1/referencia/equivalencias) y después confirma la implementación actual en el repositorio de Eco.

## Código y navegación [#código-y-navegación]

### ¿Dónde está el código principal de Eco? [#dónde-está-el-código-principal-de-eco]

La red comienza principalmente en:

```text title="Eco"
src/Assets/Pandora/Logica/Nucleo/Core/Red/
```

La estructura se divide en cliente, común, servidor y componentes relacionados con protocolo, transporte y serialización.

### ¿Por dónde debería empezar a estudiar Eco? [#por-dónde-debería-empezar-a-estudiar-eco]

La ruta recomendada es:

```text
Inicio rápido
  ↓
Fundamentos
  ↓
Modelo de red
  ↓
Comunicación
  ↓
Transporte
  ↓
Runtime
  ↓
Guías
```

<Card title="Inicio Rápido" href="/docs/red/v1">
  Recorrido inicial y modelo mental de Eco.
</Card>

<Card title="Guías" href="/docs/red/v1/guias">
  Workflows prácticos para integrar la red en un proyecto.
</Card>
