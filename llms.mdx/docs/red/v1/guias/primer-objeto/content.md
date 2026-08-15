# Primer objeto (/docs/red/v1/guias/primer-objeto)



# Primer objeto [#primer-objeto]

Esta guía muestra el paso siguiente después de conectar el cliente: trabajar con una entidad que tenga identidad de red y comportamiento asociado.

<Callout title="Modelo mental" type="info">
  En Eco, `Objeto` representa la identidad de red y `Componente` implementa el comportamiento asociado. No son conceptos intercambiables.
</Callout>

<div className="fd-steps">
  <div className="fd-step">
    ## Crear la entidad [#1-crear-la-entidad]

    Un objeto puede corresponder a un `GameObject` de Unity que tenga un `Objeto` y uno o varios `Componente`.

    ```text
    GameObject
    ├── Objeto
    ├── Componente
    └── otros componentes de Unity
    ```

    El objeto necesita un identificador de red válido para participar en la comunicación con el servidor.
  </div>

  <div className="fd-step">
    ## Añadir un componente de red [#2-añadir-un-componente-de-red]

    El patrón habitual es derivar de `Componente`:

    ```csharp
    public class MiUnidad : Componente
    {
        // Comportamiento de red de la unidad.
    }
    ```

    El componente se asocia al `Objeto` de su jerarquía y puede utilizarlo como contexto de red.
  </div>

  <div className="fd-step">
    ## Leer y modificar estado [#3-leer-y-modificar-estado]

    Para datos de estado utiliza las operaciones de `Objeto` o sus accesos de conveniencia desde `Componente`:

    ```csharp
    Set("vida", 100);
    int vida = Get<int>("vida");
    ```

    El valor se modifica localmente y Eco determina cómo debe propagarse según la autoridad del objeto y el estado de la conexión.
  </div>

  <div className="fd-step">
    ## Comprobar propiedad [#4-comprobar-propiedad]

    Antes de permitir determinadas operaciones, comprueba la propiedad del objeto:

    ```csharp
    if (ero.isMine)
    {
        Set("vida", 100);
    }
    ```

    Ser capaz de observar un objeto no implica ser su propietario.
  </div>

  <div className="fd-step">
    ## Enviar una acción [#5-enviar-una-acción]

    Cuando lo que necesitas comunicar es una acción puntual, utiliza el mecanismo de RFC disponible en el objeto:

    ```csharp
    ero.Send("Atacar", Objetivo.Otros);
    ```

    La acción no debe confundirse con el estado. Si `vida` representa un valor persistente de la entidad, es preferible sincronizar ese dato en lugar de enviar continuamente llamadas que representen su valor.
  </div>

  <div className="fd-step">
    ## Destruir el objeto [#6-destruir-el-objeto]

    El ciclo de vida de red y el ciclo de vida de Unity están relacionados, pero no son idénticos. Utiliza la API de Eco para solicitar la destrucción de un objeto de red en lugar de asumir que `Destroy(gameObject)` informa automáticamente al servidor.

    ```text
    Gameplay
       ↓
    Destrucción de red
       ↓
    Servidor
       ↓
    Notificación a participantes
    ```
  </div>

  <div className="fd-step">
    ## Probarlo dentro de un canal [#7-probarlo-dentro-de-un-canal]

    El objeto debe existir en el contexto de un canal válido. Una prueba mínima debería ser:

    ```text
    Cliente conectado
          ↓
    Canal activo
          ↓
    Objeto registrado
          ↓
    Componente activo
          ↓
    Estado / RFC
    ```

    Si alguno de estos estados falta, el comportamiento observado puede ser local aunque todavía no exista una entidad de red utilizable.
  </div>
</div>

## Errores frecuentes [#errores-frecuentes]

### Crear un `Componente` sin un `Objeto` válido [#crear-un-componente-sin-un-objeto-válido]

El componente puede resolver o crear un objeto local en determinados casos, pero eso no significa que exista automáticamente una identidad registrada en el servidor.

### Utilizar RPC para todo [#utilizar-rpc-para-todo]

Una acción y un estado son problemas diferentes. Utiliza RFC para acciones puntuales y los mecanismos de datos/sincronización para estado.

### Ignorar el ownership [#ignorar-el-ownership]

Si varios clientes modifican el mismo estado sin una autoridad clara, el resultado será difícil de razonar y sincronizar.

## Siguiente paso [#siguiente-paso]

Continúa con [Sincronización](/docs/red/v1/comunicacion/sincronizacion) para definir autoridad, frecuencia y persistencia del estado.

## Referencias [#referencias]

<Card title="Objetos" href="/docs/red/v1/modelo/objetos">
  Identidad, ownership y ciclo de vida de `Objeto`.
</Card>

<Card title="Comunicación" href="/docs/red/v1/comunicacion">
  RFC, estado y destinatarios.
</Card>

<Card title="Eco en GitHub" href="https://github.com/Nervelink/eco">
  Código fuente de la implementación actual.
</Card>
