# RFC (/docs/red/v1/comunicacion/rfc)



# RFC [#rfc]

Una RFC (*Remote Function Call*) representa una llamada a una función asociada a un `Objeto` que debe ejecutarse remotamente.

<Callout title="Acción, no estado" type="info">
  Una RFC es adecuada para comunicar que debe ocurrir algo. Para representar el valor actual de una propiedad conviene utilizar los mecanismos de estado y sincronización.
</Callout>

## Estructura [#estructura]

Eco conserva las RFC dentro del contexto del `Canal`. Cada entrada contiene, conceptualmente:

```text
RFC
├── uid
├── functionName
└── data
```

El identificador permite localizar el objeto y la función. Cuando la llamada no utiliza un identificador numérico, se conserva el nombre de la función.

## Flujo [#flujo]

```text
Componente
   ↓
Objeto
   ↓
RFC
   ↓
Paquete
   ↓
Servidor
   ↓
Destinatarios
   ↓
Objeto remoto
```

El canal forma parte del contexto de la comunicación, por lo que una RFC no debe entenderse como un mensaje global independiente de la sesión.

## RFC identificadas por nombre o ID [#rfc-identificadas-por-nombre-o-id]

Eco puede trabajar con funciones identificadas mediante un ID compacto o mediante nombre. El nombre se utiliza cuando el identificador de función es `0`.

Esto permite mantener una comunicación más compacta cuando existe un identificador estable, dejando el nombre para los casos dinámicos o no registrados mediante ID.

## Persistencia [#persistencia]

Algunas RFC pueden conservarse como parte del estado de un objeto o canal. `Canal` almacena las RFC asociadas a objetos dinámicos y puede exportarlas junto con los datos de creación del objeto.

Por tanto, una RFC persistente puede formar parte del estado necesario para reconstruir un objeto, no solamente de una cola temporal de mensajes.

## RFC desde un `Componente` [#rfc-desde-un-componente]

Los componentes trabajan normalmente a través del `Objeto` asociado:

```csharp
public class MiUnidad : Componente
{
    public void Atacar()
    {
        ero.Send("Atacar", Objetivo.Otros);
    }
}
```

`Componente` también proporciona acceso de conveniencia a la instanciación, destrucción y datos del objeto.

## Cuándo utilizar una RFC [#cuándo-utilizar-una-rfc]

Utiliza RFC cuando el mensaje representa una acción o evento puntual:

| Situación                                    | RFC               |
| -------------------------------------------- | ----------------- |
| Solicitar un ataque                          | Sí                |
| Reproducir una acción                        | Sí                |
| Ejecutar una habilidad                       | Sí                |
| Notificar un evento puntual                  | Sí                |
| Mantener `vida = 75`                         | No necesariamente |
| Sincronizar una transformación continuamente | No                |

## Relación con TNet [#relación-con-tnet]

| Eco          | TNet                    |
| ------------ | ----------------------- |
| RFC          | RFC                     |
| `Objeto`     | `TNObject`              |
| `Componente` | `TNBehaviour`           |
| `Canal`      | `TNChannel` / `Channel` |

La correspondencia sirve para trasladar conocimientos de TNet, pero la referencia normativa es el código de Eco.

## Referencias [#referencias]

<Card title="Eco" href="https://github.com/Nervelink/eco">
  Implementación actual de Eco.
</Card>

<Card title="TNet" href="https://github.com/tasharen/tnet">
  Repositorio upstream.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Referencia generada sobre el repositorio actual de TNet.
</Card>
