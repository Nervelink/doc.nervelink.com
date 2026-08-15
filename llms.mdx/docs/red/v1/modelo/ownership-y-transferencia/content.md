# Ownership y transferencia (/docs/red/v1/modelo/ownership-y-transferencia)



# Ownership y transferencia [#ownership-y-transferencia]

Un `Objeto` no es sólo un GameObject que todos pueden ver. Tiene identidad de red, canal y un propietario. Estas tres piezas permiten decidir quién puede originar determinadas operaciones y cómo se conserva la entidad cuando cambia su contexto.

## Identidad del objeto [#identidad-del-objeto]

Eco asigna un identificador de red al objeto y lo registra dentro de su canal. En el código de bajo nivel el identificador puede empaquetarse junto con el identificador de la RFC para realizar despachos eficientes.

```text
Canal + ID de Objeto
        │
        ├── Buscar Objeto
        ├── Resolver RFC
        └── Aplicar datos / eventos
```

No confíes en `GetInstanceID()` de Unity como identidad de red. Ese identificador sólo representa la instancia local del motor.

## Propietario [#propietario]

Un `Objeto` puede consultar su propietario y si pertenece al jugador local.

```csharp
if (ero.esMio)
{
    // Entrada local autorizada para el objeto.
}
```

El nombre concreto de la propiedad debe consultarse en la API de Eco de tu versión; conceptualmente, la propiedad representa la relación objeto → `Jugador` propietario.

## Autoridad no es lo mismo que visibilidad [#autoridad-no-es-lo-mismo-que-visibilidad]

Todos los jugadores de un canal pueden recibir un objeto y sus eventos. Eso no significa que todos deban tener permiso para decidir su estado.

Un patrón seguro es:

```text
Cliente propietario
        │
        ├── solicita acción
        ▼
Servidor / autoridad
        │
        ├── valida
        ├── cambia estado
        └── comunica resultado
```

Una RFC recibida correctamente no convierte sus parámetros en datos confiables. La validación de reglas de juego debe permanecer en la autoridad real.

## Transferencia entre canales [#transferencia-entre-canales]

Eco conserva la capacidad heredada de TNet de transferir un objeto a otro canal. Durante el proceso la representación local puede quedar temporalmente en estado de transferencia y el objeto puede recibir un nuevo identificador en el canal destino.

```text
Objeto #100 · Canal 10
        │
        │ transferir
        ▼
Canal 20
        │
        └── Objeto registrado con identidad válida en destino
```

El cambio de canal no debe tratarse como un simple cambio de `channelID` local. El servidor coordina la operación para que los clientes de origen y destino reciban la transición correcta.

## Jerarquías de objetos [#jerarquías-de-objetos]

Un objeto puede tener relación jerárquica con otros objetos de red. Al transferir una jerarquía, los clientes que entren posteriormente en el canal destino no necesariamente reconstruirán automáticamente todas las relaciones que dependían de la jerarquía anterior.

Cuando la jerarquía sea relevante para gameplay, conviene sincronizar explícitamente la relación padre-hijo o una ruta equivalente después de la transferencia.

## Eliminación [#eliminación]

`Objeto.DestroySelf()` representa una destrucción de red, no sólo un `Destroy(gameObject)` local. El servidor elimina el objeto de su estado y los clientes reciben la orden correspondiente.

```csharp
ero.DestroySelf();
```

Existe también una variante retardada para casos donde necesitas dejar tiempo para completar otras operaciones.

<Callout title="No uses Destroy() para entidades de red" type="warn">
  Destruir directamente el GameObject puede dejar a otros participantes con una representación fantasma. Para entidades registradas utiliza el ciclo de destrucción de `Objeto`.
</Callout>

## Relación con TNet [#relación-con-tnet]

DeepWiki identifica esta misma separación en TNet mediante `TNObject`, `owner`, `ownerID`, `uid`, `parent` y transferencia entre canales. Eco conserva el concepto porque forma parte del modelo de red, pero los tipos y nombres públicos son los de Eco.

<Card title="Objetos" href="/docs/red/v1/modelo/objetos">
  Modelo completo de las entidades de red.
</Card>

<Card title="Varios canales" href="/docs/red/v1/ejemplos/varios-canales">
  Ejemplo práctico de varios canales simultáneos.
</Card>
