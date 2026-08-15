# Enviar una acción (/docs/red/v1/guias/enviar-accion)



# Enviar una acción [#enviar-una-acción]

Una **RFC** (*Remote Function Call*) se utiliza cuando la intención es ejecutar comportamiento en otro extremo de la red. Es especialmente adecuada para acciones puntuales: atacar, recoger un objeto, reproducir un efecto, solicitar una operación o notificar un evento.

<Callout title="Estado frente a acción" type="info">
  Una RFC comunica que algo debe ocurrir. La sincronización comunica cuál es el estado actual. Si necesitas mantener `vida = 75`, sincroniza el dato; si necesitas ejecutar `RecibirDaño(25)`, una RFC representa mejor la intención.
</Callout>

## 1. Crear el comportamiento remoto [#1-crear-el-comportamiento-remoto]

El comportamiento de red vive normalmente en un `Componente` asociado a un `Objeto`.

```csharp
public class Unidad : Componente
{
    public void Atacar()
    {
        ero.Send("Atacar", Objetivo.Otros);
    }
}
```

`Componente` proporciona acceso al `Objeto` mediante `ero`, por lo que el código de gameplay no necesita construir manualmente el `Paquete`.

## 2. Enviar la acción [#2-enviar-la-acción]

El envío se realiza con `Send`, indicando el nombre de la función y el objetivo de la comunicación.

```csharp
ero.Send("Atacar", Objetivo.Otros);
```

Cuando la acción necesita argumentos, estos forman parte de la llamada:

```csharp
ero.Send("RecibirDaño", Objetivo.Otros, 25);
```

La serialización de esos argumentos termina formando parte del `Buffer` asociado al paquete de protocolo.

## 3. Elegir destinatarios [#3-elegir-destinatarios]

El objetivo determina quién debe recibir la operación. No debe asumirse que una RFC siempre significa «todos».

La elección debe responder a la semántica de la acción:

| Necesidad                                 | Destinatario habitual                 |
| ----------------------------------------- | ------------------------------------- |
| Ejecutar en otros participantes del canal | `Objetivo.Otros`                      |
| Ejecutar también localmente               | Objetivo que incluya al emisor        |
| Enviar al propietario                     | Objetivo dirigido al propietario      |
| Comunicación persistente                  | Mecanismo persistente correspondiente |

El conjunto exacto de objetivos disponibles debe consultarse en la API de Eco, ya que los nombres y combinaciones forman parte de su implementación actual.

## 4. No mezclar acción y estado [#4-no-mezclar-acción-y-estado]

Un error habitual es utilizar RFC para representar continuamente un valor que realmente es estado.

Evita un flujo como:

```text
CambiarVida(100)
CambiarVida(99)
CambiarVida(98)
CambiarVida(97)
...
```

cuando el verdadero modelo es:

```text
Objeto
└── vida = 97
```

En ese caso, `Set` y la sincronización de estado son más apropiados.

Por el contrario, una acción como:

```text
Atacar()
AbrirPuerta()
RecogerObjeto()
```

representa un evento que debe ejecutarse y no necesariamente mantenerse como estado actual.

## 5. Autoridad [#5-autoridad]

Enviar una RFC no elimina la necesidad de definir autoridad.

Por ejemplo, si un cliente solicita:

```csharp
ero.Send("RecibirDaño", Objetivo.Propietario, 25);
```

la arquitectura debe determinar quién puede realizar la acción y quién conserva la verdad del estado resultante.

Una arquitectura habitual es:

```text
Cliente atacante
      │
      │ Solicitud
      ▼
Servidor / propietario
      │
      │ valida y aplica
      ▼
Estado actualizado
      │
      └── sincronización a participantes
```

La RFC transporta la intención. La autoridad determina si la intención debe modificar el estado.

## 6. Acciones y múltiples canales [#6-acciones-y-múltiples-canales]

Una RFC está asociada al contexto del objeto y, por extensión, a su canal.

```text
Canal 10
└── Objeto 100
     └── Atacar()

Canal 20
└── Objeto 100
     └── Atacar()
```

Que dos objetos tengan el mismo identificador numérico no significa que una RFC enviada en un canal deba ejecutarse en el otro.

Antes de enviar una acción, asegúrate de que el objeto pertenece al canal correcto y que el cliente está correctamente integrado en ese canal.

## 7. Persistencia [#7-persistencia]

Eco también contempla RFC persistentes. En ese caso, la llamada no debe interpretarse simplemente como un mensaje efímero: puede formar parte del estado almacenado del canal u objeto y utilizarse posteriormente para reconstruir el estado de participantes que entren más tarde.

Utiliza una RFC persistente cuando el hecho de haber ejecutado o registrado la operación forme parte del estado que debe conservarse.

No utilices persistencia sólo porque necesites fiabilidad inmediata; son conceptos distintos.

## 8. Cuándo usar cada mecanismo [#8-cuándo-usar-cada-mecanismo]

```text
¿Estoy comunicando una acción?
        │
       sí
        ↓
       RFC

¿Estoy comunicando el estado actual de un dato?
        │
       sí
        ↓
   Set / sincronización

¿Necesito conservarlo para futuros participantes?
        │
       sí
        ↓
Estado persistente / RFC persistente
```

| Problema                              | Mecanismo                                             |
| ------------------------------------- | ----------------------------------------------------- |
| Ataque puntual                        | RFC                                                   |
| Abrir una puerta                      | RFC                                                   |
| Cambiar vida                          | Estado / sincronización                               |
| Transformación de una unidad          | Estado / sincronización                               |
| Evento que debe quedar registrado     | RFC persistente o estado persistente, según semántica |
| Datos iniciales para un nuevo jugador | Estado sincronizado / persistente                     |

## Referencia a TNet [#referencia-a-tnet]

Eco mantiene el concepto de RFC heredado de TNet, pero la API que debe utilizarse es la de `eco`.

| TNet          | Eco          |
| ------------- | ------------ |
| `TNBehaviour` | `Componente` |
| `TNObject`    | `Objeto`     |
| `Send` / RFC  | `Send` / RFC |
| `Target`      | `Objetivo`   |

TNet puede servir para localizar el origen conceptual del sistema, pero la implementación normativa es `Nervelink/eco`.

## Referencias [#referencias]

<Card title="Comunicación" href="/docs/red/v1/comunicacion">
  Explicación conceptual de RFC, destinatarios y estado.
</Card>

<Card title="Sincronización" href="/docs/red/v1/comunicacion/sincronizacion">
  Cómo mantener datos coherentes entre participantes.
</Card>

<Card title="Eco en GitHub" href="https://github.com/Nervelink/eco">
  Código fuente de la implementación actual.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream de referencia.
</Card>
