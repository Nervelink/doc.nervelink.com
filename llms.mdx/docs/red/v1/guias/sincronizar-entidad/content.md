# Sincronizar una entidad (/docs/red/v1/guias/sincronizar-entidad)



# Sincronizar una entidad [#sincronizar-una-entidad]

Una vez creado un objeto de red, el siguiente paso habitual es mantener parte de su estado sincronizado entre los participantes.

La idea central es sencilla: **el objeto tiene un estado y un propietario, y Eco se encarga de propagar los cambios según el modelo de autoridad**.

<div className="fd-steps">
  <div className="fd-step">
    ## Definir el componente [#1-definir-el-componente]

    Crea un `Componente` que represente el comportamiento de la entidad.

    ```csharp
    public class Unidad : Componente
    {
        public void CambiarVida(int valor)
        {
            Set("vida", valor);
        }
    }
    ```

    El `Componente` utiliza su `Objeto` asociado para acceder al estado de red.
  </div>

  <div className="fd-step">
    ## Escribir el estado [#2-escribir-el-estado]

    El estado puede escribirse mediante `Set`:

    ```csharp
    Set("vida", 100);
    Set("nivel", 5);
    ```

    También puedes utilizar rutas jerárquicas cuando el estado tenga más estructura:

    ```csharp
    Set("estadisticas/vida", 100);
    Set("estadisticas/defensa", 25);
    ```

    El valor se actualiza localmente antes de que Eco determine cómo debe propagarse.
  </div>

  <div className="fd-step">
    ## Leer el estado [#3-leer-el-estado]

    Cualquier participante que disponga del objeto puede consultar los datos mediante `Get`:

    ```csharp
    int vida = Get<int>("vida");
    int nivel = Get<int>("nivel");
    ```

    La lectura no implica comunicación con el servidor: consulta el estado local conocido por ese objeto.
  </div>

  <div className="fd-step">
    ## Entender la autoridad [#4-entender-la-autoridad]

    El punto más importante es decidir quién puede producir el estado.

    ```text
                     Objeto
                        │
                  propietario
                        │
              ┌─────────┴─────────┐
              │                   │
            Propietario        Otros clientes
              │                   │
           produce             consumen
            estado               estado
    ```

    Cuando el cliente local es propietario, Eco puede distribuir el cambio y actualizar el estado persistente cuando corresponde.

    Cuando el cliente no es propietario, un cambio puede requerir una solicitud al propietario en lugar de modificar unilateralmente la autoridad remota.
  </div>

  <div className="fd-step">
    ## Sincronizar un estado sencillo [#5-sincronizar-un-estado-sencillo]

    Para una propiedad discreta, el flujo normal puede ser tan simple como:

    ```csharp
    public void RecibirDaño(int daño)
    {
        int vida = Get<int>("vida");
        Set("vida", Mathf.Max(0, vida - daño));
    }
    ```

    Aquí `vida` es estado. No hace falta convertir cada valor en una llamada remota diferente como `CambiarVida(99)`, `CambiarVida(98)`, etc.
  </div>

  <div className="fd-step">
    ## ¿Qué ocurre si todavía no se puede enviar? [#6-qué-ocurre-si-todavía-no-se-puede-enviar]

    Durante determinadas fases de conexión o entrada a canales, el objeto puede existir localmente pero todavía no estar listo para transmitir.

    En ese caso, Eco puede mantener los cambios pendientes y procesarlos cuando `PuedeEnviar` vuelva a ser verdadero.

    ```text
    Set()
      ↓
    actualización local
      ↓
    ¿Puede enviar?
     ├── Sí → sincronizar
     └── No → mantener pendiente
                  ↓
            objeto preparado
                  ↓
              sincronizar
    ```

    Esto evita que el gameplay tenga que conocer todos los estados transitorios de la conexión.
  </div>

  <div className="fd-step">
    ## Sincronización automática [#7-sincronización-automática]

    Cuando una propiedad de Unity debe sincronizarse periódicamente, `AutoSincronizar` puede evitar tener que escribir manualmente el código de comparación y envío.

    Es especialmente útil durante prototipos o para componentes sencillos.

    ```text
    Transform / campo
           ↓
    AutoSincronizar
           ↓
    cambio detectado
           ↓
    Send / SendQuickly
    ```

    `AutoSincronizar` comprueba valores mediante reflexión y puede limitar la frecuencia de actualización con `updatesPerSecond`.
  </div>

  <div className="fd-step">
    ## Elegir el mecanismo correcto [#8-elegir-el-mecanismo-correcto]

    | Necesidad                                     | Mecanismo                     |
    | --------------------------------------------- | ----------------------------- |
    | Propiedad persistente de una entidad          | `Set` / estado del objeto     |
    | Acción puntual                                | RFC                           |
    | Campo de Unity durante prototipo              | `AutoSincronizar`             |
    | Estado muy frecuente y crítico                | Sistema específico optimizado |
    | Estado que debe sobrevivir a nuevos jugadores | Persistencia del estado       |

    Una regla útil es distinguir **estado** de **acción**.

    ```text
    "Mi vida es 80"   → estado
    "He atacado"      → acción
    ```

    El primero suele ser candidato a sincronización; el segundo suele expresarse mediante una comunicación de evento o RFC.
  </div>

  <div className="fd-step">
    ## Ejemplo completo [#9-ejemplo-completo]

    ```csharp
    public class Unidad : Componente
    {
        protected override void Start()
        {
            base.Start();

            if (isMine && !Has("vida"))
                Set("vida", 100);
        }

        public void RecibirDaño(int daño)
        {
            if (!isMine) return;

            int vida = Get<int>("vida");
            Set("vida", Mathf.Max(0, vida - daño));
        }

        public int ObtenerVida()
        {
            return Get<int>("vida");
        }
    }
    ```

    La idea importante del ejemplo no es la clase concreta, sino el reparto de responsabilidades:

    ```text
    Unity / gameplay
          ↓
    Componente
          ↓
    Objeto
          ↓
    Estado + propietario
          ↓
    Eco
    ```
  </div>
</div>

## Errores habituales [#errores-habituales]

### Sincronizar todo por RFC [#sincronizar-todo-por-rfc]

Generar una RFC por cada cambio de propiedad suele complicar el sistema y puede aumentar el tráfico innecesariamente.

### Ignorar la propiedad [#ignorar-la-propiedad]

Si varios clientes pueden escribir el mismo estado sin un criterio de autoridad, la documentación del estado deja de representar una única fuente de verdad.

### Usar `AutoSincronizar` para todo [#usar-autosincronizar-para-todo]

Es cómodo, pero su implementación basada en reflexión y comprobaciones periódicas puede resultar innecesaria en sistemas críticos o de alta frecuencia.

### Confundir lectura con sincronización [#confundir-lectura-con-sincronización]

`Get` devuelve el estado que el objeto conoce localmente. No es una consulta remota al servidor.

## Relación con TNet [#relación-con-tnet]

El modelo es equivalente al flujo tradicional de `TNObject` y `TNAutoSync`, pero en Eco los nombres públicos son `Objeto`, `Componente` y `AutoSincronizar`.

La referencia principal para el comportamiento sigue siendo el código de `Nervelink/eco`.

## Referencias [#referencias]

<Card title="Objeto y datos" href="/docs/red/v1/modelo/objetos">
  Identidad, propiedad y estado de los objetos de Eco.
</Card>

<Card title="Sincronización" href="/docs/red/v1/comunicacion/sincronizacion">
  Funcionamiento detallado del sistema de sincronización.
</Card>

<Card title="TNet upstream" href="https://github.com/tasharen/tnet">
  Referencia de la arquitectura original.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Referencia generada sobre el repositorio actual de TNet.
</Card>
