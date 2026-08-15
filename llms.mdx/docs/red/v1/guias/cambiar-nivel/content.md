# Cambiar el nivel de un canal (/docs/red/v1/guias/cambiar-nivel)



# Cambiar el nivel de un canal [#cambiar-el-nivel-de-un-canal]

En Eco, cambiar el nivel de un canal es una operación de red. El cambio debe coordinarse con el servidor y con el estado del canal; no es equivalente a llamar directamente a `SceneManager.LoadScene` en cada cliente.

## Cuándo utilizarlo [#cuándo-utilizarlo]

Utiliza el cambio de nivel del canal cuando todos los participantes de un canal deban pasar a un nuevo estado o escena coordinadamente.

```text
Canal
  │
  ├── Estado actual
  │
  └── Cambio de nivel
          ↓
      Servidor
          ↓
   Clientes del canal
```

## Solicitar el cambio [#solicitar-el-cambio]

Desde el cliente, la operación se realiza sobre el canal concreto:

```csharp
cliente.LoadLevel(canalID, "Arena");
```

La selección del canal es importante cuando el cliente participa en varios canales: el cambio afecta al contexto indicado y no convierte automáticamente el resto de canales en candidatos al cambio.

## Durante la transición [#durante-la-transición]

La entrada en un canal y los cambios de nivel tienen estados transitorios. Durante estas fases Eco evita que ciertas operaciones de objetos y paquetes pertenecientes al contexto anterior se procesen fuera de orden.

Por eso no conviene asumir que inmediatamente después de solicitar el cambio:

```csharp
cliente.LoadLevel(20, "Arena");
// Todo el estado de Arena ya existe aquí.
```

El código de juego debe esperar a los callbacks o estados apropiados antes de empezar a operar con el nuevo contenido.

## Cambiar de nivel no es abandonar el canal [#cambiar-de-nivel-no-es-abandonar-el-canal]

El canal continúa siendo el mismo ámbito lógico:

```text
Canal 20
├── Jugadores
├── Objetos
├── Estado
└── Nivel
      ↓
    cambio
      ↓
    Nuevo nivel
```

Por tanto, las operaciones de salida (`LeaveChannel`) y las de cambio de nivel cumplen funciones diferentes.

## Errores habituales [#errores-habituales]

### Cargar la escena directamente en Unity [#cargar-la-escena-directamente-en-unity]

Puede dejar al cliente fuera de sincronía respecto al servidor. El cambio debe pasar por la infraestructura de Eco cuando el nivel forma parte del estado del canal.

### Operar sobre objetos demasiado pronto [#operar-sobre-objetos-demasiado-pronto]

Durante la transición algunos objetos pueden no estar todavía registrados. Comprueba `hasBeenRegistered`, pertenencia al canal y los callbacks apropiados antes de utilizarlos.

### Ignorar el contexto multicanal [#ignorar-el-contexto-multicanal]

Si el cliente está en varios canales, especifica siempre el canal que debe cambiar. No presupongas que el canal activo del juego es el único contexto de red existente.

## Referencias [#referencias]

<Card title="Canales" href="/docs/red/v1/modelo/canales">
  Estado, jugadores y ciclo de vida de un canal.
</Card>

<Card title="Varios canales simultáneos" href="/docs/red/v1/guias/multiples-canales">
  Gestión de varios ámbitos de red desde una misma conexión.
</Card>
