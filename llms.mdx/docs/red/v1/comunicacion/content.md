# Comunicación (/docs/red/v1/comunicacion)



# Comunicación [#comunicación]

La comunicación de Eco se divide en dos problemas: **ejecutar algo** y **mantener algo sincronizado**. Las RFC resuelven principalmente el primero; `Set`, `Nodo` y los sistemas de sincronización resuelven el segundo.

<Callout title="Primero decide qué quieres comunicar" type="idea">
  No empieces por `Send`. Empieza por definir si estás enviando una acción, un estado, una respuesta o información que debe persistir para futuros participantes.
</Callout>

<Cards>
  <Card title="RFC" href="/docs/red/v1/comunicacion/rfc">
    Ejecutar acciones o notificaciones remotas.
  </Card>

  <Card title="Sincronización" href="/docs/red/v1/comunicacion/sincronizacion">
    Mantener propiedades y datos de una entidad coherentes entre participantes.
  </Card>

  <Card title="Destinatarios" href="/docs/red/v1/comunicacion/destinatarios">
    Determinar quién debe recibir una operación.
  </Card>

  <Card title="Comunicación avanzada" href="/docs/red/v1/comunicacion/rfc-avanzadas">
    RFC persistentes, coordinación y patrones heredados de TNet que Eco mantiene.
  </Card>
</Cards>

## Árbol de decisión [#árbol-de-decisión]

```text
¿Qué representa el mensaje?
        │
        ├── Acción puntual ───────────► RFC
        │
        ├── Propiedad / estado ───────► Set / Sync
        │
        ├── Estado para futuros users ─► persistencia
        │
        └── Operación de protocolo ───► Paquete
```

## Flujo de una RFC [#flujo-de-una-rfc]

<div className="fd-steps">
  <div className="fd-step">
    ### Definir la operación [#definir-la-operación-step]

    Declara la función remota en un `Componente` asociado a un `Objeto`.
  </div>

  <div className="fd-step">
    ### Elegir el alcance [#elegir-el-alcance-step]

    Determina si deben recibirla todos, otros jugadores, un jugador concreto o un conjunto determinado.
  </div>

  <div className="fd-step">
    ### Serializar los parámetros [#serializar-los-parámetros-step]

    Eco transforma los parámetros en datos transportables sin que el gameplay necesite construir directamente el `Buffer`.
  </div>

  <div className="fd-step">
    ### Procesar en destino [#procesar-en-destino-step]

    El receptor resuelve el objeto, localiza la RFC y ejecuta la función correspondiente.
  </div>
</div>

## Estado frente a eventos [#estado-frente-a-eventos]

<Tabs items="['RFC', 'Estado']">
  <Tab value="RFC">
    Usa RFC para hechos puntuales: disparar un arma, abrir una puerta, reproducir una animación o solicitar una acción.
  </Tab>

  <Tab value="Estado">
    Usa sincronización para valores que deben poder consultarse en cualquier momento: vida, inventario, configuración o estado de una entidad.
  </Tab>
</Tabs>

## Autoridad [#autoridad]

La comunicación no elimina la autoridad. Un cliente puede solicitar una operación, pero el servidor y el propietario del objeto siguen siendo parte del diseño de seguridad y consistencia.

<Callout title="No confíes en el cliente" type="warn">
  La validación importante debe permanecer en el lado que controla la autoridad real. Una RFC recibida correctamente no significa que la acción sea válida desde las reglas del juego.
</Callout>

## Referencias [#referencias]

<Cards>
  <Card title="RFC" href="/docs/red/v1/comunicacion/rfc">
    Sintaxis y ciclo de una llamada remota.
  </Card>

  <Card title="Sincronización" href="/docs/red/v1/comunicacion/sincronizacion">
    Estado, `Set`, `Get` y colas de sincronización.
  </Card>

  <Card title="Destinatarios" href="/docs/red/v1/comunicacion/destinatarios">
    Objetivos de una comunicación.
  </Card>

  <Card title="Guía: Enviar una acción" href="/docs/red/v1/guias/enviar-accion">
    Workflow práctico para una RFC.
  </Card>
</Cards>
