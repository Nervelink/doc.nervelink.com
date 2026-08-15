# Arquitectura (/docs/red/v1/fundamentos/arquitectura)



# Arquitectura [#arquitectura]

Eco es la capa de red utilizada por Pandora. Parte de la arquitectura de TNet, pero utiliza la nomenclatura, organización y extensiones propias de Nervelink.

<Callout title="Regla fundamental" type="info">
  Piensa en Eco como un sistema por capas. `Canal`, `Objeto`, `Jugador`, `RFC`, `Paquete` y `Buffer` resuelven problemas distintos; no son sustitutos entre sí.
</Callout>

## Mapa de la arquitectura [#mapa-de-la-arquitectura]

```text
┌─────────────────────────────────────────────────────────────┐
│                         Gameplay                            │
└────────────────────────────┬────────────────────────────────┘
                             │
                    Objeto / Componente
                             │
              ┌──────────────┴──────────────┐
              │                             │
             RFC                         Estado
              │                             │
              └──────────────┬──────────────┘
                             │
                          Canal
                             │
                  Paquete / Protocolo
                             │
                           Buffer
                             │
                    TCP / UDP / custom
                             │
              ┌──────────────┴──────────────┐
              │                             │
           Cliente                       Servidor
```

## Las cuatro capas que debes distinguir [#las-cuatro-capas-que-debes-distinguir]

<Cards>
  <Card title="Modelo de red" href="/docs/red/v1/modelo">
    Define quién participa, qué objetos existen y en qué canal viven.
  </Card>

  <Card title="Comunicación" href="/docs/red/v1/comunicacion">
    Define qué se quiere comunicar y quién debe recibirlo.
  </Card>

  <Card title="Transporte" href="/docs/red/v1/transporte">
    Convierte la operación en un mensaje serializado y lo transporta.
  </Card>

  <Card title="Runtime" href="/docs/red/v1/runtime">
    Ejecuta cliente y servidor y mantiene sus ciclos de vida.
  </Card>
</Cards>

## Modelo de red [#modelo-de-red]

### Canal [#canal]

Un `Canal` delimita un ámbito de jugadores y estado. Una conexión puede pertenecer a varios canales simultáneamente; esto es una propiedad del modelo de Eco, no una conexión física adicional.

### Jugador [#jugador]

`Jugador` representa una participación en la sesión. Su identidad es independiente de la conexión física y dispone de datos propios que pueden sincronizarse.

### Objeto [#objeto]

`Objeto` aporta identidad de red, ownership, canal, datos y ciclo de vida. Su `uid` combina el contexto del canal con la identidad del objeto.

### Componente [#componente]

`Componente` es la capa de integración con `MonoBehaviour`: permite acceder cómodamente al objeto de red y es el punto habitual desde el que se declaran RFC, datos y operaciones de instanciación.

## Comunicación [#comunicación]

Una operación empieza en gameplay y acaba convertida en un mensaje de protocolo.

```text
Componente
   │
   ├── RFC ────────────────┐
   │                       │
   └── Set / Sync ─────────┤
                           ▼
                        Objeto
                           │
                        Paquete
                           │
                         Buffer
                           │
                      Transporte
```

La selección de destinatario se realiza antes de que el mensaje abandone el contexto de Eco. Por eso [Destinatarios](/docs/red/v1/comunicacion/destinatarios) pertenece conceptualmente a Comunicación y no a Transporte.

## Runtime [#runtime]

El cliente mantiene su conexión y representación local. El servidor mantiene el estado compartido, administra jugadores y canales y procesa las solicitudes que requieren autoridad del servidor.

```text
ClienteJuego
├── Conexión TCP
├── UDP opcional
├── Canales
├── Jugador local
└── Cola / procesamiento de paquetes

ServidorJuego
├── Listener TCP
├── UDP opcional
├── Jugadores
├── Canales
├── Persistencia
└── Procesamiento de paquetes
```

## Flujo de una operación [#flujo-de-una-operación]

<div className="fd-steps">
  <div className="fd-step">
    ### Gameplay \[step] [#1-gameplay-step]

    Tu código decide qué quiere hacer: cambiar estado, ejecutar una acción, crear un objeto o cambiar de canal.
  </div>

  <div className="fd-step">
    ### Modelo de red \[step] [#2-modelo-de-red-step]

    Eco determina el `Objeto`, `Canal`, propietario y contexto de la operación.
  </div>

  <div className="fd-step">
    ### Comunicación \[step] [#3-comunicación-step]

    Se selecciona RFC, sincronización o una operación de protocolo existente y se determinan sus destinatarios.
  </div>

  <div className="fd-step">
    ### Serialización \[step] [#4-serialización-step]

    Los parámetros se convierten a una representación binaria mediante `Buffer` y las herramientas de serialización.
  </div>

  <div className="fd-step">
    ### Transporte \[step] [#5-transporte-step]

    El mensaje viaja mediante TCP, UDP cuando corresponde, o una conexión personalizada.
  </div>

  <div className="fd-step">
    ### Aplicación remota \[step] [#6-aplicación-remota-step]

    El receptor procesa el paquete, actualiza el estado correspondiente y ejecuta los callbacks asociados.
  </div>
</div>

## Relación con TNet [#relación-con-tnet]

| TNet          | Eco          | Responsabilidad                |
| ------------- | ------------ | ------------------------------ |
| `TNManager`   | `Eco`        | Fachada y operaciones globales |
| `TNObject`    | `Objeto`     | Identidad y estado de red      |
| `TNBehaviour` | `Componente` | Integración con Unity          |
| `Channel`     | `Canal`      | Ámbito compartido              |
| `Packet`      | `Paquete`    | Protocolo                      |
| `Buffer`      | `Buffer`     | Datos binarios                 |
| `Player`      | `Jugador`    | Participante                   |

<Callout title="No copies el workflow antiguo de TNet literalmente" type="warn">
  Los ejemplos históricos suelen asumir un único canal activo y una API distinta. En Eco debes seguir el comportamiento actual del repositorio y utilizar las páginas de [Modelo de red](/docs/red/v1/modelo) y [Guías](/docs/red/v1/guias) como referencia práctica.
</Callout>

## Fuentes [#fuentes]

<Cards>
  <Card title="Eco" href="https://github.com/Nervelink/eco">
    Implementación actual.
  </Card>

  <Card title="TNet upstream" href="https://github.com/tasharen/tnet">
    Código del que procede la arquitectura base.
  </Card>

  <Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
    Índice externo útil para comparar la arquitectura upstream.
  </Card>
</Cards>
