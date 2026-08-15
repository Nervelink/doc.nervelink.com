# Servidor local (/docs/red/v1/guias/servidor-local)



# Servidor local [#servidor-local]

Eco permite ejecutar `ServidorJuego` y `ClienteJuego` dentro del mismo proceso. Este modo conserva el modelo lógico de cliente/servidor, pero sustituye el transporte por una conexión interna.

<Callout title="Cuándo utilizarlo" type="info">
  El servidor local es especialmente útil para pruebas, desarrollo, herramientas internas y escenarios donde quieres ejecutar toda la infraestructura de red sin depender de otra instancia del juego ni de una conexión TCP real.
</Callout>

## Arquitectura [#arquitectura]

```text
┌─────────────────────────────────────┐
│              Unity                  │
│                                     │
│   ClienteJuego  ←→  ServidorJuego   │
│          conexión local             │
│             sin sockets              │
└─────────────────────────────────────┘
```

El cliente sigue viendo un servidor y el servidor sigue gestionando jugadores, canales y objetos. Lo que cambia es el mecanismo utilizado para transportar los datos entre ambos.

## Crear el servidor [#crear-el-servidor]

La instancia local se representa mediante `ServidorJuego`.

```csharp
var servidor = new ServidorJuego();
```

A partir de ahí se configura y arranca como un servidor normal. En el modo local no es necesario depender de un `TcpListener` para que el cliente pueda conectarse.

## Conectar el cliente [#conectar-el-cliente]

`ClienteJuego` puede conectarse directamente a una instancia de `ServidorJuego`.

```csharp
var servidor = new ServidorJuego();
var cliente = new ClienteJuego();

cliente.Connect(servidor);
```

El flujo lógico continúa siendo:

```text
ClienteJuego
    ↓
solicitud
    ↓
ServidorJuego
    ↓
respuesta
    ↓
ClienteJuego
```

La diferencia es que los datos no tienen que abandonar el proceso para llegar al otro extremo.

## Qué se mantiene igual [#qué-se-mantiene-igual]

El modo local no elimina los conceptos fundamentales de Eco. Siguen existiendo:

* `Jugador` y estado de jugador.
* `Canal` y pertenencia a canales.
* `Objeto` y `Componente`.
* RFC y destinatarios.
* Sincronización de estado.
* Persistencia y estado de servidor cuando corresponda.

Por eso es posible utilizarlo para validar buena parte de la lógica del juego antes de desplegar un servidor independiente.

## Qué cambia [#qué-cambia]

Aunque el modelo lógico sea el mismo, el entorno local no reproduce las condiciones de una red real.

```text
Servidor local
├── sin latencia de red real
├── sin pérdida real de paquetes
├── sin NAT
└── sin separación entre procesos
```

Esto significa que una prueba correcta en local no demuestra por sí sola que una implementación esté preparada para condiciones reales de Internet o de una LAN.

## Ventajas [#ventajas]

El servidor local resulta especialmente útil para:

| Caso                             | Utilidad |
| -------------------------------- | -------- |
| Pruebas automatizadas            | Alta     |
| Desarrollo rápido                | Alta     |
| Prototipos                       | Alta     |
| Herramientas de depuración       | Alta     |
| Simular varios jugadores         | Alta     |
| Medir comportamiento real de red | Baja     |

## Ejemplo: iniciar una partida local [#ejemplo-iniciar-una-partida-local]

Un patrón habitual es crear el servidor, conectar el cliente y solicitar un canal:

```csharp
var servidor = new ServidorJuego();
var cliente = new ClienteJuego();

cliente.Connect(servidor);

cliente.JoinChannel(
    channelID: 1,
    levelName: "Arena",
    persistent: false,
    playerLimit: 4,
    password: ""
);
```

Desde ese momento el resto de la lógica puede utilizar `Canal`, `Objeto`, RFC y sincronización de la misma forma que en una conexión remota.

## Servidor local y pruebas multijugador [#servidor-local-y-pruebas-multijugador]

El modo local también puede utilizarse para levantar varias instancias de cliente en un mismo proceso si la arquitectura del juego lo permite.

```text
          ServidorJuego
          /     |      \
         /      |       \
Cliente A   Cliente B   Cliente C
```

Esto permite probar:

* entrada y salida de jugadores;
* propiedad de objetos;
* sincronización;
* múltiples canales;
* RFC y destinatarios;
* creación y destrucción de objetos.

Debe tenerse en cuenta que todos los clientes comparten el mismo proceso y, por tanto, no existe el aislamiento que tendrían procesos o máquinas diferentes.

## Cuándo no utilizarlo como prueba final [#cuándo-no-utilizarlo-como-prueba-final]

El servidor local no debe ser la única validación de una funcionalidad que dependa de las propiedades reales de una red.

Antes de considerar una funcionalidad lista para producción conviene probar también:

```text
Servidor local
      ↓
LAN / servidor independiente
      ↓
Internet / condiciones reales
```

Especialmente cuando la funcionalidad depende de UDP, latencia, pérdida de paquetes, desconexiones o tiempos de respuesta.

## Relación con `Runtime` [#relación-con-runtime]

La arquitectura del servidor local pertenece al runtime, no al transporte. El transporte real puede desaparecer, pero el servidor y el cliente siguen ejecutando sus respectivas responsabilidades.

```text
Runtime
├── ClienteJuego
├── ServidorJuego
└── Servidor local
       │
       └── conexión interna
```

## Relación con TNet [#relación-con-tnet]

Eco conserva el concepto de servidor local presente en la arquitectura heredada de TNet. La nomenclatura actual utiliza `ServidorJuego` y `ClienteJuego`.

La referencia normativa sigue siendo el código de `Nervelink/eco`.

## Referencias [#referencias]

<Card title="Runtime · Cliente" href="/docs/red/v1/runtime/cliente">
  Gestión de conexiones y sesión desde el lado cliente.
</Card>

<Card title="Runtime · Servidor" href="/docs/red/v1/runtime/servidor">
  Estado autoritativo y gestión de jugadores y canales.
</Card>

<Card title="Servidor local · Eco" href="https://github.com/Nervelink/eco/tree/main/src/Assets/Pandora/Logica/Nucleo/Core/Red">
  Código fuente de la implementación actual de Eco.
</Card>

<Card title="TNet upstream" href="https://github.com/tasharen/tnet">
  Repositorio original de la arquitectura heredada.
</Card>
