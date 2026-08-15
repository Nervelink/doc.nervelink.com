# Física en red (/docs/red/v1/ejemplos/fisica-en-red)



# Física en red [#física-en-red]

La física es uno de los casos más difíciles de sincronizar porque genera cambios frecuentes y puede divergir ligeramente entre máquinas.

## Separar simulación y representación [#separar-simulación-y-representación]

```text
Autoridad
   ↓
Simulación física
   ↓
Estado reducido
   ↓
Sincronización
   ↓
Interpolación local
```

No es recomendable enviar cada contacto, fuerza o paso de `FixedUpdate` mediante una RFC fiable.

## Qué sincronizar [#qué-sincronizar]

Normalmente basta con un estado reducido:

```text
posición
rotación
velocidad
velocidad angular
estado lógico
```

Las clases `SincronizarRB` y `TransformSuave` existen precisamente para trabajar con este tipo de patrón.

<Callout title="La interpolación es parte de la red" type="info">
  Una réplica remota no necesita recibir exactamente todos los estados intermedios. Puede interpolar entre estados suficientemente frecuentes.
</Callout>

## Autoridad [#autoridad]

Define primero quién controla el cuerpo:

```text
Servidor autoritativo
       │
       ├── recibe intención
       ├── simula
       └── replica resultado
```

O, en diseños concretos, el propietario puede controlar la simulación y el servidor actuar como coordinador.

## Frecuencia [#frecuencia]

La física y la red no necesitan compartir frecuencia.

```text
Física:      50 Hz
Red:         10-20 Hz
Render:      60-144 Hz
```

La representación local interpola entre los estados recibidos.

## Anti-patrones [#anti-patrones]

<Callout title="Evita esto" type="warn">
  No envíes posición y rotación con RFC fiables en cada `FixedUpdate`. Genera tráfico, colas y latencia innecesarios y no garantiza una representación visual suave.
</Callout>

<Card title="Sincronizar Rigidbody" href="/docs/red/v1/avanzado/sincronizar-rigidbody">
  Implementación específica para cuerpos físicos.
</Card>

<Card title="TransformSuave" href="/docs/red/v1/avanzado/transform-suave">
  Interpolación y representación remota.
</Card>
