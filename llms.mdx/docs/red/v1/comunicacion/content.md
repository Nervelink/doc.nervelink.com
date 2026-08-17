# Comunicación (/docs/red/v1/comunicacion)



# Comunicación [#comunicación]

La comunicación de Eco distingue entre **creación**, **ejecución de métodos** y **sincronización de estado**. La terminología pública actual es:

```text
RCR  → Red Creación Remota
RMR  → Red Método Remoto
RCL  → Red Creación Local
```

<Callout title="Terminología normativa de Eco" type="info">
  Si vienes de TNet, verás que `RCC`, `RFC` y `LCR` han sido refactorizados en Eco como `RCR`, `RMR` y `RCL`, respectivamente. En esta documentación usamos siempre los nombres de Eco salvo cuando explicamos compatibilidad histórica.
</Callout>

<Cards>
  <Card title="RMR" href="/docs/red/v1/comunicacion/rmr">
    Ejecutar un método remotamente.
  </Card>

  <Card title="RCR" href="/docs/red/v1/comunicacion/rcr">
    Crear un objeto mediante la red.
  </Card>

  <Card title="RCL" href="/docs/red/v1/comunicacion/rcl">
    Solicitud de creación local dentro del flujo de red.
  </Card>

  <Card title="Sincronización" href="/docs/red/v1/comunicacion/sincronizacion">
    Mantener propiedades y datos de una entidad coherentes entre participantes.
  </Card>
</Cards>

## Árbol de decisión [#árbol-de-decisión]

```text
¿Qué representa la operación?
        │
        ├── Crear objeto remotamente ───► RCR
        │
        ├── Ejecutar un método remoto ──► RMR
        │
        ├── Solicitar creación local ───► RCL
        │
        ├── Mantener estado ─────────────► Set / Sync
        │
        ├── Conservar estado ────────────► Persistencia
        │
        └── Operación de protocolo ──────► Paquete
```

## Creación frente a método [#creación-frente-a-método]

Crear una entidad y ejecutar una operación sobre una entidad existente son problemas distintos.

```text
RCR / RCL
   ↓
Existe el objeto
   ↓
RMR
   ↓
Ejecuta un método
```

No utilices un método remoto como sustituto conceptual de la creación de una entidad.

## Estado frente a operaciones remotas [#estado-frente-a-operaciones-remotas]

<Tabs items="['RMR', 'Estado', 'RCR / RCL']">
  <Tab value="RMR">
    Usa `RMR` para acciones, comandos o hechos puntuales que requieren ejecutar un método remoto.
  </Tab>

  <Tab value="Estado">
    Usa sincronización para valores que deben poder consultarse en cualquier momento: vida, inventario, configuración o estado de una entidad.
  </Tab>

  <Tab value="RCR / RCL">
    Usa los mecanismos de creación cuando necesitas incorporar una entidad al modelo de red.
  </Tab>
</Tabs>

## Autoridad [#autoridad]

La comunicación no elimina la autoridad. Un cliente puede solicitar una operación, pero la autoridad de la partida y las reglas de validación siguen siendo parte del diseño de seguridad y consistencia.

<Callout title="No confíes en el cliente" type="warn">
  Que una operación llegue correctamente por red no significa que sea válida. La validación importante debe permanecer en el lado que controla la autoridad real.
</Callout>

## Relación con TNet [#relación-con-tnet]

| Eco   | TNet  | Significado         |
| ----- | ----- | ------------------- |
| `RCR` | `RCC` | Red Creación Remota |
| `RMR` | `RFC` | Red Método Remoto   |
| `RCL` | `LCR` | Red Creación Local  |

Consulta [Migración de TNet a Eco](/docs/red/v1/ingenieria/migracion-tnet-eco) para más diferencias.

## Referencias [#referencias]

<Cards>
  <Card title="RMR" href="/docs/red/v1/comunicacion/rmr">
    Red Método Remoto.
  </Card>

  <Card title="RCR" href="/docs/red/v1/comunicacion/rcr">
    Red Creación Remota.
  </Card>

  <Card title="RCL" href="/docs/red/v1/comunicacion/rcl">
    Red Creación Local.
  </Card>

  <Card title="Destinatarios" href="/docs/red/v1/comunicacion/destinatarios">
    Decide quién debe recibir una operación.
  </Card>
</Cards>
