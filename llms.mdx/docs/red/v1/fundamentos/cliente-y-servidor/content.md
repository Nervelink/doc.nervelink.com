# Cliente y servidor (/docs/red/v1/fundamentos/cliente-y-servidor)



# Cliente y servidor [#cliente-y-servidor]

La arquitectura de Eco distingue claramente el estado local del cliente de la autoridad del servidor. Esta separación aparece como una de las ideas centrales del análisis de TNet y es especialmente importante al diseñar gameplay multijugador.

## Cliente [#cliente]

`ClienteJuego` mantiene la sesión desde el lado de Unity:

```text
ClienteJuego
├── conexión
├── jugador local
├── canales locales
├── recepción de paquetes
├── estado de UDP
├── callbacks
└── objetos visibles
```

`Eco` actúa como fachada pública para que el código de juego no tenga que manipular directamente todos esos detalles.

## Servidor [#servidor]

El servidor mantiene el estado compartido y aplica operaciones que requieren autoridad:

```text
ServidorJuego
├── jugadores
├── canales
├── objetos registrados
├── persistencia
├── administración
├── recepción de paquetes
└── reenvío / procesamiento
```

El servidor no es simplemente “otro cliente con más permisos”. Es el punto que mantiene la representación compartida del mundo de red.

## Flujo cliente → servidor → clientes [#flujo-cliente--servidor--clientes]

```text
Cliente A
   │
   │ solicitud / RFC / datos
   ▼
Servidor
   │
   ├── valida
   ├── modifica estado
   ├── persiste cuando corresponde
   └── distribuye
        ├──────────────► Cliente A
        ├──────────────► Cliente B
        └──────────────► Cliente C
```

Esta es la razón por la que una operación local no debe considerarse automáticamente una operación sincronizada.

## Canal como unidad de aislamiento [#canal-como-unidad-de-aislamiento]

Los canales permiten que el servidor procese grupos independientes de jugadores y objetos. Un jugador puede pertenecer a varios canales, pero cada entidad de red sigue ligada a un contexto concreto.

```text
Servidor
├── Canal 10
│   ├── Jugador A
│   └── Objetos 1..20
│
└── Canal 20
    ├── Jugador B
    └── Objetos 21..40
```

Las operaciones de objeto deben incluir el canal correcto; no conviene inferirlo a partir de un único “canal actual” cuando hay múltiples canales activos.

## Persistencia del servidor [#persistencia-del-servidor]

En canales persistentes, Eco puede mantener información más allá de la presencia inmediata de un jugador. Los objetos persistentes forman parte de ese estado y se restauran para participantes que entran posteriormente cuando el modelo de canal lo requiere.

Esto hace que red y persistencia estén relacionadas, pero no significa que Eco sustituya el sistema de guardado específico del juego.

## Autoridad [#autoridad]

El servidor puede verificar permisos, propietario, canal y datos antes de aplicar una operación.

```csharp
// Conceptual: la solicitud llega al servidor.
// El servidor decide si la operación es válida.
```

No debes enviar desde el cliente el resultado final de una operación crítica cuando el servidor puede calcularlo por sí mismo.

## Errores de diseño frecuentes [#errores-de-diseño-frecuentes]

| Error                                | Consecuencia                                  |
| ------------------------------------ | --------------------------------------------- |
| confiar en cualquier RFC recibida    | clientes pueden falsificar acciones           |
| mantener autoridad sólo en UI/local  | divergencia entre jugadores                   |
| usar estado local como verdad global | desincronización                              |
| asumir un único canal                | operaciones dirigidas al contexto equivocado  |
| guardar todo como objeto persistente | crecimiento de estado y coste de restauración |

<Callout title="Cliente = intención; servidor = autoridad" type="idea">
  No es una regla absoluta para todas las simulaciones, pero es un excelente punto de partida para diseñar juegos cooperativos y competitivos con Eco.
</Callout>

<Card title="Canales" href="/docs/red/v1/modelo/canales">
  Aislamiento y pertenencia de jugadores.
</Card>

<Card title="Persistencia" href="/docs/red/v1/persistencia">
  Datos y objetos que sobreviven a la sesión inmediata.
</Card>
