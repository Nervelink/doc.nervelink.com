# Prueba de estrés (/docs/red/v1/ejemplos/stress-test)



# Prueba de estrés [#prueba-de-estrés]

Una prueba de estrés debe medir el sistema que realmente quieres desplegar. Eco permite observar tráfico, latencia y procesamiento, pero una prueba útil combina esas métricas con un workload controlado.

## Qué medir [#qué-medir]

<Cards>
  <Card title="Red">
    Paquetes, bytes, ping y frecuencia de envío.
  </Card>

  <Card title="CPU">
    Tiempo dedicado a procesar paquetes y serializar datos.
  </Card>

  <Card title="Memoria">
    Objetos registrados, buffers y estado persistente.
  </Card>

  <Card title="Escala">
    Jugadores, objetos y canales simultáneos.
  </Card>
</Cards>

## Escenario mínimo [#escenario-mínimo]

```text
10 clientes
  ×
100 objetos por cliente
  ×
10 actualizaciones/s
  ×
RFC ocasionales
```

No empieces con cientos de jugadores. Primero identifica el comportamiento de una sola variable y aumenta la carga de forma incremental.

## Metodología [#metodología]

<Steps>
  <Step>
    ### Línea base [#línea-base]

    Ejecuta el escenario sin tráfico adicional y registra CPU, memoria y latencia.
  </Step>

  <Step>
    ### Añadir objetos [#añadir-objetos]

    Incrementa la cantidad de entidades sin cambiar todavía la frecuencia de sincronización.
  </Step>

  <Step>
    ### Añadir frecuencia [#añadir-frecuencia]

    Aumenta las actualizaciones por segundo y observa cuándo empieza a crecer la cola de entrada.
  </Step>

  <Step>
    ### Añadir jugadores [#añadir-jugadores]

    Repite el experimento con varias conexiones y canales.
  </Step>

  <Step>
    ### Encontrar el cuello de botella [#encontrar-el-cuello-de-botella]

    Comprueba si el límite aparece en CPU, serialización, ancho de banda, servidor o cliente.
  </Step>
</Steps>

## Métricas de Eco [#métricas-de-eco]

```csharp
Debug.Log($"Ping: {Eco.Ping} ms");
Debug.Log($"Enviados: {Eco.PaquetesEnviados} / {Eco.BytesEnviados} bytes");
Debug.Log($"Recibidos: {Eco.PaquetesRecibidos} / {Eco.BytesRecibidos} bytes");
```

## Qué no medir de forma aislada [#qué-no-medir-de-forma-aislada]

Un ping bajo no garantiza un buen rendimiento. Una aplicación puede tener baja latencia y, aun así, saturar CPU o ancho de banda.

<Callout title="El objetivo es el workload real" type="idea">
  Una simulación artificial de muchos paquetes pequeños puede ser peor o mejor que una partida real. Replica la distribución de RFC, sincronización, creación y destrucción de objetos que tendrá tu juego.
</Callout>

<Card title="Diagnosticar una conexión" href="/docs/red/v1/guias/diagnosticar-una-conexion">
  Workflow para obtener una primera fotografía del estado de red.
</Card>
