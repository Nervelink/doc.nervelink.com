# Persistir una partida (/docs/red/v1/guias/persistir-partida)



# Persistir una partida [#persistir-una-partida]

Eco permite conservar el estado de los canales y de sus objetos para que una sesión pueda restaurarse posteriormente. La persistencia debe entenderse como una propiedad del estado de red: no sustituye al guardado propio del juego, sino que proporciona un mecanismo para conservar el estado que Eco administra.

<Callout title="Idea principal" type="info">
  Un objeto o canal persistente puede sobrevivir a la desconexión de los jugadores y volver a estar disponible cuando el estado se restaura. Decide explícitamente qué parte del estado pertenece a Eco y qué parte debe seguir guardándose mediante tu sistema de partidas.
</Callout>

## Qué puede persistir [#qué-puede-persistir]

Dentro del modelo de Eco, la persistencia está relacionada especialmente con:

```text
Servidor
└── Canal
    ├── Datos del canal
    ├── Objetos creados
    │   └── Datos de cada objeto
    ├── RFC guardadas
    └── Objetos destruidos
```

El canal mantiene estructuras separadas para objetos creados, objetos destruidos y RFC persistentes. Esto permite reconstruir el estado de una sesión cuando vuelve a cargarse.

## Objetos persistentes [#objetos-persistentes]

Al crear un objeto dinámico puede indicarse que debe conservarse:

```csharp
Eco.Instanciar(
    canalID,
    rccID,
    null,
    "Jugador",
    true,
    datos
);
```

El parámetro `persistent` indica que el objeto forma parte del estado que el servidor debe conservar.

La persistencia es apropiada para entidades que representan estado del mundo, no para efectos efímeros como partículas, proyectiles o eventos visuales.

## Datos persistentes [#datos-persistentes]

El estado de un objeto puede almacenarse mediante su `Nodo`:

```csharp
objeto.Set("vida", 75);
objeto.Set("oro", 1200);
objeto.Set("nivel", 8);
```

La sincronización y la persistencia son conceptos relacionados, pero no idénticos:

```text
Set()
  ↓
Estado local
  ↓
Sincronización
  ↓
Estado compartido
  ↓
Persistencia del servidor
```

Un dato puede necesitar sincronizarse en tiempo real pero no conservarse después de destruir el objeto. Del mismo modo, un estado persistente puede necesitar cargarse para un nuevo jugador aunque no haya cambiado en ese momento.

## RFC persistentes [#rfc-persistentes]

Eco también mantiene RFC guardadas asociadas al canal. Son útiles cuando un comportamiento o configuración de red debe estar disponible para participantes que entren posteriormente.

No deben utilizarse como sustituto genérico de un sistema de eventos. Una RFC persistente representa una condición o información que debe formar parte del estado recuperable del canal.

## Guardado del servidor [#guardado-del-servidor]

`ServidorJuego` mantiene funciones de lectura y escritura del estado del mundo y utiliza un archivo de estado por defecto, configurable mediante la infraestructura del servidor.

```text
Canales activos
      ↓
Estado del servidor
      ↓
Guardar
      ↓
world.dat / almacenamiento configurado
```

La implementación permite sustituir el almacenamiento por callbacks propios. Esto permite integrar Eco con un sistema de guardado externo si el juego necesita bases de datos, archivos propios o almacenamiento remoto.

## Cargar el estado [#cargar-el-estado]

Al arrancar el servidor, el estado persistente puede restaurarse antes de que entren los jugadores. El flujo conceptual es:

```text
Iniciar servidor
      ↓
Leer almacenamiento
      ↓
Restaurar canales
      ↓
Restaurar objetos
      ↓
Aceptar jugadores
```

La restauración debe producirse antes de asumir que un canal representa un mundo nuevo.

## Dormir y despertar canales [#dormir-y-despertar-canales]

Eco también contempla el ciclo `Sleep` / `Wake` para canales inactivos. El objetivo es reducir el uso de memoria de canales que no tienen jugadores activos sin perder su estado persistente.

```text
Canal activo
    ↓
Sin jugadores
    ↓
Sleep
    ↓
Estado conservado
    ↓
Nuevo jugador
    ↓
Wake
    ↓
Canal activo
```

Esto resulta especialmente útil en servidores que mantienen muchos mundos o partidas persistentes.

## Qué no deberías persistir [#qué-no-deberías-persistir]

No todo lo que existe en una escena debe convertirse en estado persistente.

| Tipo de dato                       | Persistir                               |
| ---------------------------------- | --------------------------------------- |
| Inventario                         | Sí, si forma parte del estado del mundo |
| Progreso de misión                 | Sí                                      |
| Posición de una entidad permanente | Habitualmente sí                        |
| Proyectil en vuelo                 | Normalmente no                          |
| Efecto visual                      | No                                      |
| Evento puntual                     | No                                      |
| Cache temporal                     | No                                      |

La persistencia debe representar el mundo, no el historial completo de cada frame.

## Persistencia frente a guardado del juego [#persistencia-frente-a-guardado-del-juego]

En un juego real probablemente tendrás dos capas:

```text
            Guardado del juego
                  │
        ┌─────────┴─────────┐
        │                   │
   Datos de jugador     Datos de partida

                Eco
                  │
        ┌─────────┴─────────┐
        │                   │
      Canal              Objetos
      Estado             Estado red
```

Eco debe conservar aquello que necesita reconstruir su propio estado de red. El sistema de guardado del juego debe conservar datos de negocio como inventario, progreso, economía, estadísticas y configuración.

## Recomendación de diseño [#recomendación-de-diseño]

Antes de marcar un objeto como persistente, define:

```text
¿Debe existir cuando nadie está conectado?
        ↓
¿Debe restaurarse después de reiniciar el servidor?
        ↓
¿Representa estado del mundo?
        ↓
Sí → Persistente
No → Temporal
```

Esta decisión evita llenar el almacenamiento con objetos cuya vida real dura unos pocos segundos.

## Relación con TNet [#relación-con-tnet]

La persistencia de canales, objetos creados, RFC guardadas y estado del servidor procede del modelo de TNet que Eco conserva y renombra.

Para conocer los nombres equivalentes y los cambios realizados por Eco, consulta la sección de [Referencia](/docs/red/v1/referencia).

## Referencias [#referencias]

<Card title="Persistencia del servidor" href="/docs/red/v1/runtime/servidor">
  Cómo `ServidorJuego` mantiene y restaura el estado de los canales.
</Card>

<Card title="Sincronización" href="/docs/red/v1/comunicacion/sincronizacion">
  Cómo se transmite el estado antes y durante la persistencia.
</Card>

<Card title="Objetos" href="/docs/red/v1/modelo/objetos">
  Identidad, propietario y ciclo de vida de los objetos persistentes.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream de la arquitectura heredada.
</Card>
