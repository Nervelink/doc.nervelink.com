# Ingeniería (/docs/red/v1/ingenieria)



Esta sección explica cómo tomar decisiones de arquitectura alrededor de Eco. No sustituye la referencia API: responde a una pregunta diferente, **cómo diseñar el sistema para que la red sea mantenible**.

<Cards>
  <Card title="Modelo mental" href="/docs/red/v1/ingenieria/modelo-mental">
    Cómo pensar en autoridad, estado, eventos y transporte.
  </Card>

  <Card title="Sincronización" href="/docs/red/v1/ingenieria/sincronizacion">
    Cómo decidir qué estado viaja por la red y con qué frecuencia.
  </Card>

  <Card title="Rendimiento" href="/docs/red/v1/ingenieria/rendimiento">
    Ancho de banda, frecuencia, entidades y coste de serialización.
  </Card>

  <Card title="Seguridad" href="/docs/red/v1/ingenieria/seguridad">
    Validación del cliente y superficie de ataque.
  </Card>

  <Card title="Testing" href="/docs/red/v1/ingenieria/testing">
    Pruebas con múltiples clientes, latencia y desconexiones.
  </Card>

  <Card title="Diagnóstico" href="/docs/red/v1/ingenieria/diagnostico">
    Método sistemático para encontrar problemas de red.
  </Card>
</Cards>

## Principio central [#principio-central]

Eco debe transportar el estado y las decisiones que necesitan distribución. La arquitectura del juego sigue perteneciendo al juego.

```text
Dominio del juego
      ↓
Estado autoritativo
      ↓
Adaptador de red
      ↓
Eco
      ↓
Clientes
      ↓
Presentación local
```

Si una funcionalidad no puede explicar quién es su autoridad, qué estado necesita sincronizar, quién debe recibirlo y qué ocurre al desconectarse un jugador, todavía no está suficientemente diseñada.
