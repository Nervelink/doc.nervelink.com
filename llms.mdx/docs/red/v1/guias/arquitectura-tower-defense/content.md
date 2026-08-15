# Arquitectura de un Tower Defense multijugador (/docs/red/v1/guias/arquitectura-tower-defense)



<Callout title="Objetivo de esta guía" type="info">
  Esta guía no enseña a programar una implementación concreta. Define cómo repartir responsabilidades entre cliente, servidor, Eco y gameplay para construir un Tower Defense 3D real de hasta cuatro jugadores.
</Callout>

## La decisión principal [#la-decisión-principal]

La arquitectura recomendada es **servidor autoritativo con clientes predictivos sólo donde aporte valor visual**.

El cliente expresa intención: seleccionar, construir, mejorar, vender, mover una unidad controlable o activar una habilidad. El servidor valida la intención, modifica el estado real de la partida y Eco distribuye ese estado a los jugadores correspondientes.

No conviene utilizar Eco como un simple sistema para replicar cada `Transform`. Eco debe transportar el estado y las decisiones que necesitan autoridad de red; el cliente debe encargarse de presentar ese estado de forma fluida.

<Callout title="Regla de oro" type="warn">
  Si una decisión puede cambiar el resultado de la partida, el servidor debe poder reconstruirla y validarla sin confiar en el cliente.
</Callout>

<div className="fd-steps">
  <div className="fd-step">
    ## Arquitectura completa [#1-arquitectura-completa]

    ```text
                             ┌─────────────────────┐
                             │   Servidor de juego │
                             │                     │
                             │ Estado autoritativo │
                             │ Simulación combate  │
                             │ Economía            │
                             │ Oleadas             │
                             │ IA                  │
                             │ Validación          │
                             └──────────┬──────────┘
                                        │
                                  Eco / Red
                                        │
                  ┌─────────────────────┼─────────────────────┐
                  │                     │                     │
            ┌─────▼─────┐         ┌─────▼─────┐         ┌─────▼─────┐
            │ Cliente 1 │         │ Cliente 2 │         │ Cliente 3 │
            │ jugador   │         │ jugador   │         │ jugador   │
            └─────┬─────┘         └─────┬─────┘         └─────┬─────┘
                  │                     │                     │
                  └─────────────────────┼─────────────────────┘
                                        │
                                  Cliente 4
    ```

    Cada cliente contiene aproximadamente cuatro capas:

    <Cards>
      <Card title="Presentación" href="/docs/red/v1/fundamentos/arquitectura">
        Cámara, UI, animaciones, efectos, audio y feedback visual.
      </Card>

      <Card title="Intención del jugador" href="/docs/red/v1/comunicacion">
        Acciones que el jugador solicita al servidor.
      </Card>

      <Card title="Representación de red" href="/docs/red/v1/modelo/objetos">
        Objetos y componentes que representan el estado recibido.
      </Card>

      <Card title="Simulación local" href="/docs/red/v1/sincronizacion">
        Interpolación, suavizado y efectos que no necesitan autoridad.
      </Card>
    </Cards>
  </div>

  <div className="fd-step">
    ## Qué debe ser autoritativo [#2-qué-debe-ser-autoritativo]

    El servidor debe poseer cualquier dato que pueda afectar al resultado de la partida.

    | Sistema                 | Autoridad recomendada | Motivo                                   |
    | ----------------------- | --------------------- | ---------------------------------------- |
    | Economía                | Servidor              | Evita generar recursos arbitrariamente   |
    | Construcción            | Servidor              | Valida coste, posición y reglas          |
    | Mejoras                 | Servidor              | Impide saltarse costes o requisitos      |
    | Vida de unidades        | Servidor              | El daño debe ser verificable             |
    | Daño                    | Servidor              | Resultado competitivo                    |
    | IA                      | Servidor              | Todos deben observar la misma decisión   |
    | Oleadas                 | Servidor              | El tiempo y composición deben ser únicos |
    | Objetivos               | Servidor              | Evita selección manipulada               |
    | Proyectiles de gameplay | Servidor              | Su resultado puede cambiar el combate    |
    | Animaciones             | Cliente               | Son presentación                         |
    | VFX                     | Cliente               | No necesitan sincronización              |
    | Audio                   | Cliente               | Es presentación                          |
    | Cámara                  | Cliente               | Es completamente local                   |
    | UI                      | Cliente               | Presenta el estado recibido              |

    <Callout title="No sincronices lo que puede reconstruirse" type="info">
      Una explosión, una estela, una animación de ataque o una barra de selección no deberían convertirse automáticamente en estado de red. Sincroniza el hecho relevante y deja que cada cliente reconstruya la presentación.
    </Callout>
  </div>

  <div className="fd-step">
    ## El modelo de datos [#3-el-modelo-de-datos]

    Antes de crear objetos de red conviene separar **definición**, **estado** y **presentación**.

    ```text
    RazaAsset
    UnidadAsset
    TorreAsset
    ProyectilAsset
    HabilidadAsset
            │
            ▼
       Definición estática
            │
            ▼
     Estado de partida
            │
            ├── vida
            ├── nivel
            ├── propietario
            ├── posición
            ├── objetivo
            ├── cooldown
            └── estados
            │
            ▼
     Presentación local
            ├── modelo
            ├── animación
            ├── partículas
            └── audio
    ```

    Los `ScriptableObject` son una buena ubicación para estadísticas, costes, modelos, iconos, curvas y reglas que no cambian durante la partida. El estado que cambia durante una partida debe pertenecer al runtime de esa partida.

    No conviene sincronizar un `ScriptableObject` completo. Se sincronizan identificadores y valores de estado; cada cliente resuelve localmente la definición correspondiente.
  </div>

  <div className="fd-step">
    ## Razas [#4-razas]

    Una raza debe tratarse como **contenido estático + configuración de partida**.

    Ejemplo conceptual:

    ```text
    Raza Elfa
    ├── torres disponibles
    ├── unidades disponibles
    ├── mejoras
    ├── habilidades
    └── modificadores
    ```

    El servidor determina qué raza ha elegido el jugador y qué contenido tiene disponible.

    El cliente puede cargar todos los `RazaAsset` localmente, pero no debe decidir por sí mismo que una unidad está desbloqueada. El servidor valida la elección utilizando el identificador de raza y sus reglas.

    ### Qué sincronizar [#qué-sincronizar]

    * Identificador de raza.
    * Configuración elegida por el jugador.
    * Desbloqueos que sean específicos de la partida.
    * Cambios de raza permitidos durante el lobby, si existen.

    ### Qué no sincronizar [#qué-no-sincronizar]

    * Nombre de la raza.
    * Sprite.
    * Prefab visual.
    * Estadísticas estáticas que ya forman parte de la misma versión del juego.
  </div>

  <div className="fd-step">
    ## Unidades [#5-unidades]

    Una unidad de gameplay debe ser un **Objeto de Eco con componentes especializados**, no un único componente gigante.

    ```text
    Objeto
    └── Unidad
        ├── Identidad
        ├── Propiedad
        ├── Estado
        ├── Movimiento
        ├── Combate
        ├── Vida
        └── Habilidades
    ```

    La separación permite que cada componente tenga una responsabilidad clara y evita que la lógica de red se convierta en un monolito.

    ### Estado mínimo de red [#estado-mínimo-de-red]

    Una unidad puede necesitar:

    * posición lógica;
    * orientación relevante;
    * propietario;
    * vida actual;
    * estado de combate;
    * objetivo actual cuando otros clientes necesiten conocerlo;
    * habilidad activa;
    * estados alterados relevantes;
    * identificador de definición.

    La posición visual no tiene por qué ser idéntica al valor recibido. El cliente puede interpolarla para evitar movimiento entrecortado.
  </div>

  <div className="fd-step">
    ## Torres [#6-torres]

    Las torres son especialmente adecuadas para separar **estado persistente** de **eventos de combate**.

    ```text
    Torre
    ├── configuración
    ├── nivel
    ├── vida
    ├── propietario
    ├── objetivo actual
    ├── cooldown
    └── mejoras
    ```

    Una torre no necesita enviar cada frame que está apuntando.

    Un modelo más eficiente es:

    ```text
    Servidor
      │
      ├── Objetivo = Unidad 42
      ├── Estado = Atacando
      └── Tiempo siguiente ataque
              │
              ▼
           Cliente
              │
              ├── gira la torre
              ├── reproduce animación
              └── muestra VFX
    ```
  </div>

  <div className="fd-step">
    ## Proyectiles: dos conceptos distintos [#7-proyectiles-dos-conceptos-distintos]

    Ésta es una de las decisiones más importantes del diseño.

    Hay que diferenciar **proyectil de gameplay** y **proyectil visual**.

    ### Proyectil de gameplay [#proyectil-de-gameplay]

    Existe cuando su trayectoria o impacto forma parte de las reglas del juego.

    Ejemplos:

    * proyectil que puede fallar;
    * proyectil que cambia de objetivo;
    * misil que puede ser interceptado;
    * proyectil con área de impacto dependiente de su posición;
    * proyectil que debe persistir durante varios segundos.

    En esos casos el servidor mantiene el estado relevante.

    ### Proyectil visual [#proyectil-visual]

    Es sólo la representación de un resultado que ya ha sido decidido.

    ```text
    Servidor:
    Torre 12 impacta a Unidad 42
    Daño = 35
            │
            ▼
          Eco
            │
            ▼
    Cliente:
    crear flecha visual
    reproducir impacto
    mostrar partículas
    ```

    Este segundo modelo es preferible para ataques simples y repetitivos porque evita inundar la red con cientos de proyectiles.

    <Callout title="Regla práctica para Tower Defense" type="info">
      Si el jugador no puede cambiar el resultado interactuando con el proyectil, normalmente no necesitas sincronizar el proyectil visual. Sincroniza el resultado del ataque.
    </Callout>
  </div>

  <div className="fd-step">
    ## Combate [#8-combate]

    El combate debe vivir conceptualmente en el servidor:

    ```text
    Oleada
       ↓
    IA
       ↓
    Selección de objetivo
       ↓
    Ataque
       ↓
    Resolución de daño
       ↓
    Efectos
       ↓
    Muerte
       ↓
    Recompensa
    ```

    El cliente no debe decidir:

    * cuánto daño hace una torre;
    * si un enemigo muere;
    * cuánto oro recibe el jugador;
    * si un proyectil acertó cuando el resultado es autoritativo;
    * cuándo termina una oleada.

    El cliente sí puede decidir cómo presentar todo ello.
  </div>

  <div className="fd-step">
    ## Sistemas y subsistemas [#9-sistemas-y-subsistemas]

    La red no debería convertirse en el lugar donde vive toda la arquitectura del juego.

    Una separación razonable es:

    ```text
    Juego
    ├── Partida
    │   ├── Economía
    │   ├── Oleadas
    │   ├── Combate
    │   └── Victoria / derrota
    │
    ├── Entidades
    │   ├── Torres
    │   ├── Unidades
    │   ├── Proyectiles
    │   └── Héroes
    │
    ├── Contenido
    │   ├── Razas
    │   ├── Unidades
    │   ├── Torres
    │   └── Habilidades
    │
    ├── Presentación
    │   ├── UI
    │   ├── Cámara
    │   ├── Animación
    │   └── VFX
    │
    └── Red
        ├── Eco
        ├── Objetos
        ├── Canales
        ├── RFC
        └── Sincronización
    ```

    Eco debe ser una infraestructura transversal. No debería contener la lógica completa de Economía, IA o Combate.
  </div>

  <div className="fd-step">
    ## Qué mecanismo de Eco utilizar [#10-qué-mecanismo-de-eco-utilizar]

    La decisión puede resumirse así:

    | Necesidad                    | Mecanismo                     |
    | ---------------------------- | ----------------------------- |
    | Construir torre              | RFC / acción solicitada       |
    | Mejorar torre                | RFC / acción solicitada       |
    | Vender torre                 | RFC / acción solicitada       |
    | Cambiar objetivo manualmente | RFC                           |
    | Vida actual                  | Estado sincronizado           |
    | Nivel de torre               | Estado sincronizado           |
    | Oro del jugador              | Estado sincronizado           |
    | Unidad viva/muerta           | Estado + evento necesario     |
    | Efecto visual                | Local                         |
    | Animación de ataque          | Local                         |
    | Resultado de daño            | Estado/evento de gameplay     |
    | Inicio de oleada             | Estado/evento                 |
    | Chat                         | RFC o sistema de comunicación |
    | Guardado                     | Persistencia                  |

    La regla es: **RFC para intención o acción; sincronización para estado; persistencia para estado que debe sobrevivir; presentación para todo lo que pueda reconstruirse localmente**.
  </div>

  <div className="fd-step">
    ## Destinatarios [#11-destinatarios]

    No todo cambio debe enviarse a los cuatro jugadores.

    Por ejemplo:

    ```text
    Construcción propia
            │
            ├── propietario
            └── resto de jugadores si necesitan verla
    ```

    La visibilidad debe formar parte del diseño del estado.

    Una partida cooperativa puede necesitar que todos vean todas las torres y unidades, mientras que una habilidad exclusivamente informativa puede requerir una actualización únicamente para su propietario.

    Usa `Objetivo` para expresar esa intención en lugar de crear canales adicionales para cada pequeño caso.
  </div>

  <div className="fd-step">
    ## Canales para una partida de cuatro jugadores [#12-canales-para-una-partida-de-cuatro-jugadores]

    Una conexión puede participar en más de un canal, por lo que no es necesario tratar lobby y partida como dos procesos completamente independientes.

    Un diseño razonable es:

    ```text
    Conexión del jugador
           │
           ├── Canal Lobby
           │     ├── jugadores
           │     ├── ready
           │     └── selección de raza
           │
           └── Canal Partida
                 ├── mapa
                 ├── torres
                 ├── unidades
                 ├── oleadas
                 └── estado de partida
    ```

    Cuando comienza la partida, el canal de partida pasa a ser el ámbito de gameplay.

    No conviene utilizar un canal diferente para cada jugador. Los canales representan **ámbitos de simulación**, no permisos individuales.
  </div>

  <div className="fd-step">
    ## Flujo de conexión [#13-flujo-de-conexión]

    <Steps>
      <Step>
        El cliente establece la conexión con el servidor y obtiene su identidad de red.
      </Step>

      <Step>
        El jugador entra en el canal de lobby y recibe el estado necesario para representar la sala.
      </Step>

      <Step>
        El jugador selecciona raza y configuración. El cliente envía una intención; el servidor valida las opciones.
      </Step>

      <Step>
        Cuando los jugadores están preparados, el servidor crea o activa el canal de partida.
      </Step>

      <Step>
        El servidor crea el estado inicial de la partida y los objetos de red necesarios.
      </Step>

      <Step>
        Los clientes reciben el estado y construyen su representación visual local.
      </Step>
    </Steps>
  </div>

  <div className="fd-step">
    ## Construcción de una torre [#14-construcción-de-una-torre]

    El flujo recomendado es:

    ```text
    Jugador
      │
      │ solicita construir TorreArquera en Celda 15
      ▼
    Cliente
      │
      │ RFC / intención
      ▼
    Servidor
      │
      ├── ¿jugador válido?
      ├── ¿raza permite la torre?
      ├── ¿tiene recursos?
      ├── ¿posición válida?
      ├── ¿celda disponible?
      └── ¿reglas de partida permiten construir?
           │
           ├── NO → rechazar
           │
           └── SÍ
               ├── descontar recursos
               ├── crear Objeto
               ├── establecer propietario
               └── sincronizar estado
                        │
                        ▼
                     Clientes
    ```

    El cliente puede mostrar una previsualización verde/roja antes de enviar la solicitud. Esa previsualización no tiene autoridad.
  </div>

  <div className="fd-step">
    ## Mejorar una torre [#15-mejorar-una-torre]

    Una mejora es otra intención del jugador.

    ```text
    Cliente
      ↓
    Solicitar mejora
      ↓
    Servidor
      ↓
    Validar torre + jugador + coste + requisitos
      ↓
    Aplicar nueva configuración
      ↓
    Sincronizar nivel/estado
      ↓
    Clientes actualizan presentación
    ```

    La definición de niveles puede permanecer en `ScriptableObject`. Sólo se sincroniza el identificador o nivel actual.
  </div>

  <div className="fd-step">
    ## Movimiento de unidades [#16-movimiento-de-unidades]

    Para unidades con movimiento continuo, hay tres niveles de información:

    ### Nivel 1: destino [#nivel-1-destino]

    El servidor determina el destino o ruta lógica.

    ### Nivel 2: estado de movimiento [#nivel-2-estado-de-movimiento]

    El servidor mantiene posición lógica, velocidad, estado y navegación.

    ### Nivel 3: presentación [#nivel-3-presentación]

    El cliente interpola y anima la unidad.

    No conviene enviar transformaciones de todas las unidades en cada `Update` de Unity.

    En un Tower Defense con cientos de enemigos, esa decisión puede ser la diferencia entre una red sostenible y una tormenta de paquetes.
  </div>

  <div className="fd-step">
    ## IA [#17-ia]

    La IA debe ejecutarse en el servidor.

    ```text
    Servidor
    ├── Spawn
    ├── Pathfinding
    ├── Targeting
    ├── Attack decision
    ├── Ability decision
    └── Death
    ```

    Los clientes reciben el resultado relevante.

    Si el pathfinding es costoso, puede existir una capa de simulación que procese grupos de unidades o actualice decisiones a una frecuencia inferior a la representación visual.

    No es necesario que cada enemigo tenga una RPC por cada decisión interna de IA.
  </div>

  <div className="fd-step">
    ## Oleadas [#18-oleadas]

    La oleada es un sistema de partida, no una colección de comandos de red.

    El servidor mantiene:

    ```text
    Oleada
    ├── índice
    ├── estado
    ├── tiempo
    ├── composición
    ├── unidades generadas
    └── resultado
    ```

    Los clientes pueden mostrar una cuenta atrás basada en el estado recibido.

    La cuenta atrás visual puede interpolarse localmente; el instante real de comienzo lo determina el servidor.
  </div>

  <div className="fd-step">
    ## Economía compartida [#19-economía-compartida]

    Para un modo cooperativo conviene decidir explícitamente si los recursos son:

    ```text
    Jugador
    └── Oro individual
    ```

    o:

    ```text
    Partida
    └── Oro compartido
    ```

    La elección afecta al modelo de red.

    Los recursos individuales deben vivir en el estado del jugador. Los recursos compartidos deben vivir en el estado de la partida o canal.

    Nunca conviene mantener una segunda copia autoritativa del oro en UI, cliente y servidor.
  </div>

  <div className="fd-step">
    ## Habilidades [#20-habilidades]

    Una habilidad puede dividirse en:

    ```text
    HabilidadAsset
        │
        ├── definición estática
        └── reglas

    HabilidadRuntime
        │
        ├── cooldown
        ├── cargas
        ├── estado
        └── propietario
    ```

    El jugador envía la intención de activar la habilidad.

    El servidor valida:

    * propietario;
    * cooldown;
    * recursos;
    * objetivo;
    * alcance;
    * estado de partida;
    * requisitos de raza/unidad.

    Después resuelve el efecto y sincroniza el resultado.
  </div>

  <div className="fd-step">
    ## Desconexión de un jugador [#21-desconexión-de-un-jugador]

    Debe existir una política explícita.

    Para cada objeto propiedad del jugador hay que decidir:

    ```text
    Desconexión
       │
       ├── destruir
       ├── transferir al servidor
       ├── transferir a otro jugador
       └── mantener temporalmente
    ```

    En una partida cooperativa normalmente interesa que una torre ya construida no desaparezca simplemente porque el jugador perdió la conexión. La propiedad de red y la propiedad de gameplay no tienen por qué ser exactamente la misma cosa.
  </div>

  <div className="fd-step">
    ## Reconexión [#22-reconexión]

    La reconexión debe reconstruir el estado desde el servidor, no intentar reproducir todas las acciones ocurridas durante la desconexión.

    ```text
    Reconexión
       ↓
    Identidad
       ↓
    Canal
       ↓
    Estado persistente
       ↓
    Objetos
       ↓
    Estado de partida
       ↓
    Presentación local
    ```

    Por eso es importante que el servidor mantenga un estado suficientemente completo para que un cliente nuevo pueda reconstruir la partida.
  </div>

  <div className="fd-step">
    ## Persistencia [#23-persistencia]

    Para una partida guardable, no se debe guardar la representación visual.

    Guardar:

    * mapa o identificador de nivel;
    * semilla si forma parte del diseño;
    * día/oleada;
    * jugadores;
    * razas;
    * recursos;
    * torres;
    * niveles;
    * unidades que deban sobrevivir;
    * objetivos persistentes;
    * estado de partida necesario.

    No guardar como estado de gameplay:

    * partículas;
    * posición de una cámara;
    * animación actual de una torre;
    * proyectiles puramente visuales;
    * efectos temporales reconstruibles.

    Eco dispone de mecanismos de persistencia que deben utilizarse para el estado que realmente necesita sobrevivir, no como sustituto de un sistema de guardado de todo el juego.
  </div>

  <div className="fd-step">
    ## Rendimiento para cuatro jugadores [#24-rendimiento-para-cuatro-jugadores]

    El límite de cuatro jugadores no es el principal problema. En un Tower Defense, el verdadero coste puede estar en el número de entidades.

    El presupuesto debe analizarse como:

    ```text
    4 jugadores
    ×
    100-500 entidades
    ×
    frecuencia de actualización
    ×
    bytes por estado
    ```

    No todas las entidades deben tener la misma frecuencia.

    Un posible modelo:

    | Entidad            | Frecuencia conceptual |
    | ------------------ | --------------------- |
    | Jugador            | Baja/media            |
    | Torre              | Baja/media            |
    | Héroe              | Media/alta            |
    | Enemigo cercano    | Media/alta            |
    | Enemigo lejano     | Baja                  |
    | Proyectil gameplay | Según necesidad       |
    | VFX                | 0 red                 |
    | UI                 | 0 red                 |

    La distancia, relevancia y estado de combate deben influir en qué se sincroniza y cuándo.
  </div>

  <div className="fd-step">
    ## Qué evitar [#25-qué-evitar]

    <Callout title="Antipatrones" type="error">
      No conviertas cada `Update()` en una llamada de red. No hagas al cliente autoritativo sobre oro, daño o muertes. No sincronices VFX. No uses canales para solucionar problemas que pertenecen a destinatarios. No serialices assets completos. No hagas una RFC para cada cambio visual.
    </Callout>

    ### Antipatrón: red como arquitectura del juego [#antipatrón-red-como-arquitectura-del-juego]

    Incorrecto:

    ```text
    Eco
    └── Toda la lógica del juego
    ```

    Preferible:

    ```text
    Juego
    ├── Dominio
    ├── Sistemas
    ├── Presentación
    └── Adaptador de red
            └── Eco
    ```

    ### Antipatrón: replicar Transform sin pensar [#antipatrón-replicar-transform-sin-pensar]

    Un `Transform` es una representación visual. La red debería transportar el estado que permite reconstruirlo.

    ### Antipatrón: RPC para todo [#antipatrón-rpc-para-todo]

    Las RFC son excelentes para acciones y comandos. No son un sustituto de un modelo de estado.
  </div>

  <div className="fd-step">
    ## Organización del proyecto [#26-organización-del-proyecto]

    Una estructura razonable sería:

    ```text
    Juego
    ├── Contenido
    │   ├── Razas
    │   ├── Torres
    │   ├── Unidades
    │   ├── Proyectiles
    │   └── Habilidades
    │
    ├── Logica
    │   ├── Partida
    │   ├── Combate
    │   ├── Oleadas
    │   ├── Economia
    │   ├── IA
    │   └── Entidades
    │
    ├── Presentacion
    │   ├── UI
    │   ├── Animacion
    │   ├── VFX
    │   └── Camara
    │
    └── Red
        ├── Eco
        ├── Adaptadores
        ├── Objetos
        ├── RFC
        └── Sincronizacion
    ```

    La carpeta de red no debería absorber las reglas del dominio. Su función es conectar el dominio con Eco.
  </div>

  <div className="fd-step">
    ## Workflow de desarrollo recomendado [#27-workflow-de-desarrollo-recomendado]

    <Steps>
      <Step>
        Diseña primero el juego para un solo jugador y define qué estado es realmente necesario para reconstruir una partida.
      </Step>

      <Step>
        Separa definiciones estáticas de estado mutable. Los assets representan reglas y contenido; el runtime representa la partida.
      </Step>

      <Step>
        Clasifica cada interacción como intención, estado persistente, evento visual o dato local.
      </Step>

      <Step>
        Introduce Eco alrededor de las entidades que realmente necesitan identidad de red: jugadores, torres, unidades, estado de partida y otros objetos relevantes.
      </Step>

      <Step>
        Convierte las acciones del jugador en solicitudes que el servidor pueda validar.
      </Step>

      <Step>
        Mueve al servidor economía, combate, IA, oleadas y reglas de victoria/derrota.
      </Step>

      <Step>
        Añade sincronización de estado e interpolación visual. No intentes resolver todavía todos los casos extremos.
      </Step>

      <Step>
        Añade lobby y canales. Mantén el lobby y la partida como ámbitos distintos cuando tengan estados y ciclos de vida distintos.
      </Step>

      <Step>
        Prueba con dos jugadores, después cuatro, y finalmente con el número máximo de entidades previsto.
      </Step>

      <Step>
        Añade persistencia y reconexión cuando el modelo autoritativo ya sea estable.
      </Step>
    </Steps>
  </div>

  <div className="fd-step">
    ## Orden de implementación [#28-orden-de-implementación]

    No intentaría construir toda la infraestructura multijugador antes de tener gameplay.

    El orden recomendado es:

    ```text
    1. Partida local
          ↓
    2. Modelo de entidades
          ↓
    3. Separación estado / presentación
          ↓
    4. Servidor autoritativo
          ↓
    5. Jugador + construcción
          ↓
    6. Torres + unidades
          ↓
    7. Combate
          ↓
    8. Oleadas
          ↓
    9. Sincronización
          ↓
    10. Lobby + 2 jugadores
          ↓
    11. 4 jugadores
          ↓
    12. Persistencia
          ↓
    13. Reconexión
          ↓
    14. Optimización
    ```
  </div>

  <div className="fd-step">
    ## Criterio para saber si la arquitectura está funcionando [#29-criterio-para-saber-si-la-arquitectura-está-funcionando]

    Una buena arquitectura con Eco debería permitir responder estas preguntas sin mirar código de red:

    * ¿Quién es autoritativo sobre este dato?
    * ¿Este dato es estado o evento?
    * ¿Puede reconstruirse localmente?
    * ¿Quién necesita recibirlo?
    * ¿Debe persistir?
    * ¿Pertenece al jugador, al canal o a un objeto?
    * ¿Qué ocurre si el jugador se desconecta?
    * ¿Puede un cliente manipularlo sin pasar por el servidor?
    * ¿Cuánto cuesta sincronizarlo si existen 500 entidades?

    Si una funcionalidad necesita una respuesta distinta para cada clase de objeto, probablemente está mezclando dominio y transporte.
  </div>

  <div className="fd-step">
    ## Resumen de la arquitectura [#30-resumen-de-la-arquitectura]

    ```text
                        ┌──────────────────┐
                        │   Servidor Eco   │
                        │                  │
                        │ Partida          │
                        │ Economía         │
                        │ Oleadas          │
                        │ Combate          │
                        │ IA               │
                        │ Autoridad        │
                        └────────┬─────────┘
                                 │
                           Estado / RFC
                                 │
                  ┌──────────────┼──────────────┐
                  │              │              │
              Cliente 1      Cliente 2      Cliente 3/4
                  │              │              │
            ┌─────┴─────┐  ┌─────┴─────┐  ┌─────┴─────┐
            │ Present.  │  │ Present.  │  │ Present.  │
            │ Cámara    │  │ Cámara    │  │ Cámara    │
            │ UI        │  │ UI        │  │ UI        │
            │ VFX       │  │ VFX       │  │ VFX       │
            └───────────┘  └───────────┘  └───────────┘
    ```

    La idea central es sencilla: **Eco sincroniza una simulación de juego; no convierte automáticamente un juego local en multijugador**. El trabajo de ingeniería consiste en decidir qué parte del juego debe ser autoritativa, qué estado debe viajar por la red y qué puede reconstruirse localmente.

    Para un Tower Defense cooperativo de cuatro jugadores, esa separación permite mantener una simulación coherente en el servidor, una red razonable aunque existan cientos de entidades y clientes visualmente fluidos sin convertir cada animación en tráfico de red.
  </div>
</div>
