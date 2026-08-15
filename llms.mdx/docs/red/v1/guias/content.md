# Guías (/docs/red/v1/guias)



# Guías [#guías]

Las guías parten de una tarea concreta y terminan en una integración funcional. Cada una indica qué conceptos necesitas conocer, qué decisiones debes tomar y cómo comprobar que el resultado es correcto.

<Callout title="Orden recomendado" type="info">
  Empieza por `Primer cliente`, continúa con `Primer objeto` y `Sincronizar una entidad`. A partir de ahí puedes saltar directamente al workflow que necesites.
</Callout>

## Fundamentos de uso [#fundamentos-de-uso]

<Cards>
  <Card title="Primer cliente" href="/docs/red/v1/guias/primer-cliente">
    Conecta, espera el handshake y entra en un canal correctamente.
  </Card>

  <Card title="Primer objeto" href="/docs/red/v1/guias/primer-objeto">
    Crea una entidad y entiende su identidad de red.
  </Card>

  <Card title="Sincronizar una entidad" href="/docs/red/v1/guias/sincronizar-entidad">
    Mantén estado sincronizado respetando ownership y autoridad.
  </Card>
</Cards>

## Partidas y sesiones [#partidas-y-sesiones]

<Cards>
  <Card title="Crear una partida" href="/docs/red/v1/guias/crear-partida">
    Une conexión, canal, nivel, objetos y sincronización en un workflow completo.
  </Card>

  <Card title="Varios canales simultáneos" href="/docs/red/v1/guias/multiples-canales">
    Mantén varios ámbitos de red sobre una única conexión.
  </Card>

  <Card title="Servidor local" href="/docs/red/v1/guias/servidor-local">
    Ejecuta cliente y servidor en el mismo proceso para pruebas.
  </Card>

  <Card title="Cambiar el nivel de un canal" href="/docs/red/v1/guias/cambiar-nivel">
    Coordina cambios de escena dentro del contexto del canal.
  </Card>
</Cards>

## Objetos y autoridad [#objetos-y-autoridad]

<Cards>
  <Card title="Instanciar y destruir" href="/docs/red/v1/guias/instanciar-destruir">
    Gestiona objetos dinámicos, persistencia y destrucción.
  </Card>

  <Card title="Transferir propiedad" href="/docs/red/v1/guias/transferir-propiedad">
    Cambia el jugador propietario de un objeto dinámico.
  </Card>

  <Card title="Transferir entre canales" href="/docs/red/v1/guias/transferir-objeto-canal">
    Mueve un objeto dinámico entre ámbitos de red.
  </Card>

  <Card title="Persistir una partida" href="/docs/red/v1/guias/persistir-partida">
    Diseña un estado que pueda sobrevivir a la ausencia temporal de jugadores.
  </Card>
</Cards>

## Comunicación y datos [#comunicación-y-datos]

<Cards>
  <Card title="Enviar una acción" href="/docs/red/v1/guias/enviar-accion">
    Implementa una RFC y selecciona sus destinatarios.
  </Card>

  <Card title="Sincronizar datos del jugador" href="/docs/red/v1/guias/sincronizar-jugador">
    Separa `playerData` del estado de los objetos.
  </Card>

  <Card title="Transferir archivos" href="/docs/red/v1/guias/transferir-archivos">
    Utiliza las operaciones de archivos integradas en Eco.
  </Card>
</Cards>

## Diagnóstico y ciclo de vida [#diagnóstico-y-ciclo-de-vida]

<Cards>
  <Card title="Desconectar y limpiar" href="/docs/red/v1/guias/desconectar-limpiar">
    Cierra la sesión y limpia dependencias locales de forma ordenada.
  </Card>

  <Card title="Diagnosticar una conexión" href="/docs/red/v1/guias/diagnosticar-conexion">
    Aísla problemas de gameplay, protocolo, datos y transporte.
  </Card>
</Cards>

## Cómo utilizar una guía [#cómo-utilizar-una-guía]

<div className="fd-steps">
  <div className="fd-step">
    ### Identifica el objetivo [#identifica-el-objetivo-step]

    No copies el ejemplo de forma mecánica. Define qué quieres que ocurra en red y quién tiene autoridad.
  </div>

  <div className="fd-step">
    ### Comprueba los prerrequisitos [#comprueba-los-prerrequisitos-step]

    Abre la documentación conceptual enlazada al principio si todavía no conoces el canal, objeto, RFC o transporte implicado.
  </div>

  <div className="fd-step">
    ### Implementa el mínimo [#implementa-el-mínimo-step]

    Empieza por el flujo más pequeño que pueda validarse. Añade persistencia, ownership y optimizaciones sólo cuando la funcionalidad base sea correcta.
  </div>

  <div className="fd-step">
    ### Verifica el resultado [#verifica-el-resultado-step]

    Prueba siempre con al menos dos participantes cuando la funcionalidad sea multijugador. El modo servidor local sirve para iterar, pero no sustituye una prueba de red real.
  </div>
</div>

## Referencia relacionada [#referencia-relacionada]

<Card title="Fundamentos" href="/docs/red/v1/fundamentos">
  Conceptos y arquitectura detrás de estos workflows.
</Card>
