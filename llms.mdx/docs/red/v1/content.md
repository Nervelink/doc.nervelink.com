# Inicio Rápido (/docs/red/v1)



<Callout title="Fuente de verdad" type="info">
  Esta documentación describe el comportamiento actual del repositorio `Nervelink/eco`. TNet y DeepWiki se utilizan para contexto y equivalencias, pero no sustituyen al código de Eco.
</Callout>

## Qué necesitas entender [#qué-necesitas-entender]

Eco separa cuatro conceptos que conviene no mezclar:

<Cards>
  <Card title="Canal" href="/docs/red/v1/modelo/canales">
    Define el ámbito de jugadores y estado compartido. Una conexión puede participar en varios canales.
  </Card>

  <Card title="Objeto" href="/docs/red/v1/modelo/objetos">
    Identidad de red, ownership, datos y ciclo de vida de una entidad.
  </Card>

  <Card title="Comunicación" href="/docs/red/v1/comunicacion">
    RFC, sincronización, destinatarios y decisiones sobre qué información enviar.
  </Card>

  <Card title="Runtime" href="/docs/red/v1/runtime">
    Conexión, cliente, servidor y modo local.
  </Card>
</Cards>

## Requisitos antes de empezar [#requisitos-antes-de-empezar]

<Tabs items="['Cliente', 'Servidor', 'Local']">
  <Tab value="Cliente">
    Necesitas el código de cliente de Eco integrado en tu proyecto y acceso al punto de entrada `Eco`.
  </Tab>

  <Tab value="Servidor">
    Necesitas una instancia de `ServidorJuego` y su configuración de persistencia/transporte correspondiente.
  </Tab>

  <Tab value="Local">
    Puedes utilizar el servidor local para probar cliente y servidor dentro del mismo proceso, sin sockets.
  </Tab>
</Tabs>

Para restricciones de plataforma y comportamiento todavía dependiente del proyecto, consulta [Requisitos y limitaciones](/docs/red/v1/requisitos).

## Primer recorrido [#primer-recorrido]

<div className="fd-steps">
  <div className="fd-step">
    ### Entender la arquitectura \[step] [#1-entender-la-arquitectura-step]

    Lee [Arquitectura](/docs/red/v1/fundamentos/arquitectura) y [Conceptos](/docs/red/v1/fundamentos/conceptos). Debes distinguir conexión, canal, jugador, objeto y transporte antes de empezar a construir gameplay sobre la red.
  </div>

  <div className="fd-step">
    ### Conectar un cliente \[step] [#2-conectar-un-cliente-step]

    Sigue [Primer cliente](/docs/red/v1/guias/primer-cliente) para establecer la conexión y comprobar el estado de sesión.
  </div>

  <div className="fd-step">
    ### Crear una entidad de red \[step] [#3-crear-una-entidad-de-red-step]

    Continúa con [Primer objeto](/docs/red/v1/guias/primer-objeto) y después [Sincronizar una entidad](/docs/red/v1/guias/sincronizar-entidad).
  </div>

  <div className="fd-step">
    ### Elegir el mecanismo de comunicación \[step] [#4-elegir-el-mecanismo-de-comunicación-step]

    Consulta [Comunicación](/docs/red/v1/comunicacion). Como regla general: usa RFC para acciones y `Set`/sincronización para estado.
  </div>

  <div className="fd-step">
    ### Construir una partida real \[step] [#5-construir-una-partida-real-step]

    Cuando el flujo básico funcione, sigue [Crear una partida](/docs/red/v1/guias/crear-partida) y [Varios canales simultáneos](/docs/red/v1/guias/multiples-canales).
  </div>
</div>

## Modelo mental [#modelo-mental]

```text
                 Eco
                  │
     ┌────────────┼────────────┐
     │            │            │
  Cliente      Servidor      Común
     │            │            │
     └───────┬────┴────┬───────┘
             │         │
          Canales    Transporte
             │         │
        ┌────┴───┐   TCP / UDP
        │        │
     Objetos  Jugadores
        │
   Componentes
        │
 RFC / Estado
```

## Qué leer después [#qué-leer-después]

<Cards>
  <Card title="Comunicación" href="/docs/red/v1/comunicacion">
    Decide entre RFC, estado sincronizado y destinatarios.
  </Card>

  <Card title="Transporte" href="/docs/red/v1/transporte">
    Comprende `Paquete`, `Buffer`, serialización y TCP/UDP.
  </Card>

  <Card title="Guías" href="/docs/red/v1/guias">
    Workflows completos para integrar Eco en un juego.
  </Card>

  <Card title="Referencia" href="/docs/red/v1/referencia">
    Equivalencias con TNet, upstream y diferencias de Eco.
  </Card>
</Cards>
