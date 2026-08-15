# Crear una partida (/docs/red/v1/guias/crear-partida)



# Crear una partida [#crear-una-partida]

Una partida en Eco puede entenderse como la combinación de una conexión de red, un canal y el estado de juego que vive dentro de ese canal.

```text
Conexión
   ↓
Canal
   ↓
Nivel / escena
   ↓
Objetos de red
   ↓
Estado sincronizado
```

## 1. Conectar el cliente [#1-conectar-el-cliente]

El cliente necesita establecer primero una conexión con el servidor.

```csharp
ClienteJuego cliente = ...;
cliente.Connect(endpoint);
```

Antes de intentar entrar en la partida conviene esperar a que `isConnected` sea verdadero.

## 2. Crear o seleccionar el canal [#2-crear-o-seleccionar-el-canal]

El servidor es quien mantiene el estado del canal. El cliente solicita entrar mediante `JoinChannel`.

```csharp
cliente.JoinChannel(
    channelID: 10,
    levelName: "Arena",
    persistent: false,
    playerLimit: 4,
    password: ""
);
```

La unión no es instantánea. Eco mantiene la solicitud como pendiente hasta recibir la respuesta del servidor.

<Callout title="El canal es el ámbito de la partida" type="info">
  Los jugadores, objetos dinámicos y estado asociado a la partida deben entenderse dentro del contexto del canal. La conexión puede permanecer activa mientras el cliente cambia de un canal a otro o participa en varios simultáneamente.
</Callout>

## 3. Esperar la entrada al canal [#3-esperar-la-entrada-al-canal]

No es recomendable empezar a crear lógica de partida inmediatamente después de llamar a `JoinChannel`. Primero debe completarse la unión y, cuando corresponda, la carga del nivel asociado.

```text
JoinChannel()
     ↓
Solicitud pendiente
     ↓
Respuesta del servidor
     ↓
Canal activo
     ↓
Nivel preparado
```

Durante esta transición Eco protege el flujo de paquetes para evitar que operaciones pertenecientes a una escena anterior sean procesadas fuera de contexto.

## 4. Crear el estado de la partida [#4-crear-el-estado-de-la-partida]

Una vez dentro del canal se pueden crear los objetos dinámicos que representan el estado de la partida.

Por ejemplo:

```text
Canal
├── Jugadores
├── Objetos de la partida
│   ├── Objetivo
│   ├── Unidad
│   └── Estado de partida
└── Datos persistentes
```

Los objetos deben tener un propietario claro cuando su estado vaya a ser modificado por un cliente.

## 5. Sincronizar el estado inicial [#5-sincronizar-el-estado-inicial]

El estado que necesitan los demás jugadores debe modelarse como datos de red, no como una sucesión de acciones.

```csharp
objeto.Set("fase", "Preparacion");
objeto.Set("tiempo", 30);
```

Si el estado debe actualizarse de forma continua puede utilizarse un sistema de sincronización apropiado. Para prototipos sencillos, `AutoSincronizar` puede servir como punto de partida.

## 6. Enviar acciones de juego [#6-enviar-acciones-de-juego]

Las acciones puntuales de la partida deben utilizar el sistema de comunicación correspondiente.

```text
Estado
  → Set / sincronización

Acción
  → RFC
```

Por ejemplo, cambiar la vida actual de una unidad representa un cambio de estado, mientras que solicitar que una unidad ejecute una habilidad representa una acción.

## 7. Un segundo jugador entra [#7-un-segundo-jugador-entra]

La partida debe poder reconstruirse desde el estado del canal y de sus objetos.

```text
Jugador A
   │
   └── crea estado
            ↓
         Canal
            ↓
Jugador B entra
            ↓
  estado inicial sincronizado
```

Dependiendo de cómo se configure el objeto o componente, el estado puede conservarse en servidor o ser enviado explícitamente al nuevo participante.

## 8. Finalizar la partida [#8-finalizar-la-partida]

Al terminar una partida pueden utilizarse distintos niveles de limpieza:

```text
Salir del canal
     ↓
conservar conexión

Desconectar
     ↓
finalizar sesión completa

Eliminar canal
     ↓
eliminar el ámbito de partida
```

La elección depende de si el cliente debe continuar conectado a otros canales o sesiones.

## Flujo completo [#flujo-completo]

```text
Cliente conecta
      ↓
Servidor acepta
      ↓
JoinChannel
      ↓
Canal activo
      ↓
Carga de nivel
      ↓
Creación de objetos
      ↓
Sincronización del estado inicial
      ↓
Juego
      ├── acciones → RFC
      └── estado   → sincronización
      ↓
Fin de partida
      ↓
LeaveChannel / DeleteChannel
```

## Errores habituales [#errores-habituales]

### Crear objetos antes de completar la unión [#crear-objetos-antes-de-completar-la-unión]

Puede producir objetos locales que todavía no están correctamente registrados en el contexto de red de la partida.

### Utilizar RFC para representar todo el estado [#utilizar-rfc-para-representar-todo-el-estado]

Las RFC son adecuadas para acciones. El estado debe modelarse como estado para que un jugador que entre posteriormente pueda obtener los valores actuales.

### Confundir desconexión con salida del canal [#confundir-desconexión-con-salida-del-canal]

Salir de un canal no obliga a desconectar al cliente. Eco permite mantener la conexión y continuar en otros canales.

### No definir la autoridad [#no-definir-la-autoridad]

Si varios clientes pueden modificar el mismo dato sin una autoridad clara, el estado puede entrar en conflicto. Define el propietario antes de diseñar la sincronización.

## Véase también [#véase-también]

<Card title="Canales" href="/docs/red/v1/modelo/canales">
  Ciclo de vida y gestión de los canales.
</Card>

<Card title="Objetos" href="/docs/red/v1/modelo/objetos">
  Identidad, propiedad y ciclo de vida de los objetos de red.
</Card>

<Card title="Sincronización" href="/docs/red/v1/comunicacion/sincronizacion">
  Cómo mantener el estado coherente entre participantes.
</Card>

<Card title="Comunicación" href="/docs/red/v1/comunicacion">
  RFC, destinatarios y comunicación de alto nivel.
</Card>
