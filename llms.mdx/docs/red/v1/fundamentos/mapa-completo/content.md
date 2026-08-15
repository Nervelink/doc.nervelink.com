# Mapa completo del sistema (/docs/red/v1/fundamentos/mapa-completo)



# Mapa completo del sistema [#mapa-completo-del-sistema]

DeepWiki separa TNet en gestión de cliente, objetos, sincronización, servidor, transporte, serialización, herramientas, ejemplos y extensiones. Esa clasificación es útil para entender Eco, pero debe reinterpretarse con los nombres y responsabilidades actuales del proyecto.

## Correspondencia conceptual [#correspondencia-conceptual]

| Concepto upstream                 | Eco                                  | Función actual                                                   |
| --------------------------------- | ------------------------------------ | ---------------------------------------------------------------- |
| `TNManager`                       | `Eco`                                | Fachada global del cliente y operaciones de red                  |
| `TNObject`                        | `Objeto`                             | Identidad, ownership, canal, datos y comunicación de una entidad |
| `TNBehaviour`                     | `Componente`                         | Integración de `MonoBehaviour` con `Objeto`                      |
| `TNPlayer`                        | `Jugador`                            | Identidad y datos del participante                               |
| `Channel`                         | `Canal`                              | Ámbito lógico de jugadores y objetos                             |
| `Packet`                          | `Paquete`                            | Tipo de mensaje del protocolo                                    |
| `Buffer`                          | `Buffer`                             | Lectura y escritura binaria                                      |
| `DataNode`                        | `Nodo`                               | Datos jerárquicos y serialización estructurada                   |
| `GameClient`                      | `ClienteJuego`                       | Estado y comunicación del cliente                                |
| `TNServerInstance` / `GameServer` | `Servidor` / `ServidorJuego`         | Hosting y lógica de servidor                                     |
| `TNUpdater`                       | `ActualizadorRed`                    | Scheduler central de callbacks y actualización                   |
| `Target`                          | `Objetivo`                           | Destino de una comunicación                                      |
| `TNSyncRigidbody`                 | `SincronizarRB`                      | Sincronización específica de física                              |
| `IConnection`                     | `IConnection`                        | Transporte personalizado                                         |
| `TNServerInstance` editor         | `Servidor` + herramientas de Pandora | Integración de servidor dentro del proyecto                      |

## Capas [#capas]

```text
┌────────────────────────────────────────────────────────┐
│ Gameplay                                               │
├────────────────────────────────────────────────────────┤
│ Eco / Componente / Objeto                              │
├────────────────────────────────────────────────────────┤
│ RFC · Sync · Datos · Objetivo                          │
├────────────────────────────────────────────────────────┤
│ Paquete · Buffer · Serialización                       │
├────────────────────────────────────────────────────────┤
│ TCP · UDP · IConnection                                │
├────────────────────────────────────────────────────────┤
│ ClienteJuego              ServidorJuego                │
└────────────────────────────────────────────────────────┘
```

## Subsystems que conviene conocer [#subsystems-que-conviene-conocer]

### Gestión de sesión [#gestión-de-sesión]

`Eco` expone conexión, desconexión, estado de red, nombre e identidad del jugador, canales, administración, aliases y callbacks.

### Entidades de red [#entidades-de-red]

`Objeto` representa una entidad sincronizada. `Componente` es la fachada Unity que facilita acceder a ella y registrar comportamiento remoto.

### Comunicación [#comunicación]

Las RFC permiten ejecutar métodos remotos. Los métodos de datos y sincronización mantienen estado. `Objetivo` decide el ámbito de distribución.

### Persistencia [#persistencia]

Los canales persistentes y los objetos persistentes permiten que el servidor conserve estado de una sesión y lo reproduzca cuando corresponde. Esto no debe confundirse con guardar la partida completa de gameplay.

### Transporte [#transporte]

TCP proporciona el canal fiable principal. UDP se utiliza para tráfico donde perder un mensaje concreto puede ser preferible a acumular latencia. `IConnection` permite sustituir el transporte sin cambiar la API de alto nivel.

### Serialización [#serialización]

`Buffer` es la representación binaria de trabajo. `Nodo` ofrece una representación jerárquica para datos estructurados. Las interfaces de serialización permiten soportar tipos propios cuando una simple escritura de primitivas no es suficiente.

### Runtime [#runtime]

`ActualizadorRed` concentra `OnStart`, `OnUpdate`, `OnLateUpdate`, actualizaciones infrecuentes, callbacks de un solo uso, invocaciones retardadas y tareas basadas en enumeradores.

### Herramientas y extensiones [#herramientas-y-extensiones]

El sistema incluye integración con Steam, UPnP, runtime C#, lobby y transportes personalizados. Estos son extensiones de Eco, no requisitos del núcleo.

## Orden recomendado para aprender Eco [#orden-recomendado-para-aprender-eco]

<Steps>
  <Step>
    <div className="fd-steps">
      <div className="fd-step">
        ### Modelo de red [#1-modelo-de-red]

        Aprende `Jugador`, `Canal`, `Objeto` y `Componente`.
      </div>
    </div>
  </Step>

  <Step>
    <div className="fd-steps">
      <div className="fd-step">
        ### Comunicación [#2-comunicación]

        Aprende RFC, sincronización y `Objetivo`.
      </div>
    </div>
  </Step>

  <Step>
    <div className="fd-steps">
      <div className="fd-step">
        ### Transporte [#3-transporte]

        Aprende `Paquete`, `Buffer`, serialización y la diferencia entre TCP y UDP.
      </div>
    </div>
  </Step>

  <Step>
    <div className="fd-steps">
      <div className="fd-step">
        ### Runtime [#4-runtime]

        Comprende `Eco`, `ClienteJuego`, `ServidorJuego` y `ActualizadorRed`.
      </div>
    </div>
  </Step>

  <Step>
    <div className="fd-steps">
      <div className="fd-step">
        ### Persistencia y extensiones [#5-persistencia-y-extensiones]

        Añade datos persistentes, múltiples canales, Steam, UPnP o `IConnection` cuando el proyecto lo necesite.
      </div>
    </div>
  </Step>
</Steps>

<Callout title="Esta página no es una copia de DeepWiki" type="info">
  DeepWiki se utiliza como índice de arquitectura de TNet. Las definiciones de Eco se basan en la implementación actual del repositorio `Nervelink/eco`; cuando una idea upstream no coincide con Eco, prevalece el código de Eco.
</Callout>

<Cards>
  <Card title="Arquitectura" href="/docs/red/v1/fundamentos/arquitectura">
    Flujo entre las capas.
  </Card>

  <Card title="Ciclo de vida" href="/docs/red/v1/fundamentos/ciclo-de-vida">
    Arranque, actualización, escenas y apagado.
  </Card>

  <Card title="Referencia API" href="/docs/red/v1/referencia">
    Búsqueda por clases y miembros públicos.
  </Card>
</Cards>
