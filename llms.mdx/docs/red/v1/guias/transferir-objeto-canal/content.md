# Transferir un objeto entre canales (/docs/red/v1/guias/transferir-objeto-canal)



# Transferir un objeto entre canales [#transferir-un-objeto-entre-canales]

Eco permite transferir un objeto de red a otro canal. La operación no debe tratarse como un simple cambio local de `channelID`: el objeto cambia de contexto de red y la implementación debe coordinar la operación con el servidor.

<Callout title="Importante" type="warn">
  Durante una transferencia el objeto puede quedar temporalmente marcado como destruido para impedir que se envíen RFC en un estado intermedio. Espera a que la transferencia termine antes de asumir que el objeto está listo para comunicarse en el nuevo canal.
</Callout>

## Qué cambia [#qué-cambia]

Un `Objeto` pertenece siempre a un canal concreto y su identificador completo utiliza ambos datos:

```text
UID = Canal + ID
```

Por eso moverlo de un canal a otro no es simplemente actualizar una variable local. El nuevo contexto forma parte de la identidad de red del objeto.

## Ciclo de transferencia [#ciclo-de-transferencia]

El flujo conceptual es:

```text
Objeto en Canal A
      ↓
solicitud de transferencia
      ↓
servidor valida la operación
      ↓
objeto cambia de canal / identidad
      ↓
callback de transferencia
      ↓
Objeto en Canal B
```

Durante el proceso Eco evita tratar el objeto como completamente operativo hasta que la transferencia ha finalizado.

## Detectar una transferencia [#detectar-una-transferencia]

`Objeto` expone el callback `onTransfer`, que recibe el nuevo canal y el nuevo ID:

```csharp
objeto.onTransfer = (nuevoCanal, nuevoID) =>
{
    Debug.Log($"Transferido a {nuevoCanal} con ID {nuevoID}");
};
```

Esto permite reconstruir referencias locales que dependan del canal o del identificador dinámico.

## El `uid` puede cambiar [#el-uid-puede-cambiar]

Como `uid` combina el canal y el ID:

```csharp
ulong uid = objeto.uid;
```

no conviene guardar un `uid` como referencia permanente si el objeto puede transferirse. Después de la operación, vuelve a consultar `uid`, `channelID` e `id`.

## Objetos estáticos frente a dinámicos [#objetos-estáticos-frente-a-dinámicos]

La propiedad de los objetos estáticos está restringida y no deben tratarse igual que los objetos dinámicos. Para transferencias dinámicas, el ciclo de vida debe mantenerse coordinado con el servidor.

## Referencias locales [#referencias-locales]

Evita asumir que una referencia de un sistema externo sigue siendo válida sólo porque la instancia de Unity no ha cambiado. El objeto puede seguir siendo la misma instancia, pero su contexto de red habrá cambiado.

```text
Referencia local
       │
       ▼
   Objeto Unity
       │
       ├── channelID ← puede cambiar
       ├── id        ← puede cambiar
       └── uid       ← cambia con los anteriores
```

## Qué no hacer [#qué-no-hacer]

No cambies manualmente `channelID` para simular una transferencia. Tampoco continúes enviando mensajes usando el contexto antiguo mientras la operación está pendiente.

La transferencia es una operación del modelo de red, no una reasignación de datos de gameplay.

## Cuándo utilizarlo [#cuándo-utilizarlo]

Un caso habitual es mover entidades entre ámbitos de juego:

```text
Canal Lobby
     ↓
Canal Partida
     ↓
Canal Resultado
```

La utilidad depende de que el objeto deba mantener continuidad lógica entre esos ámbitos. Cuando no sea necesario conservar la identidad, puede ser más sencillo destruir el objeto anterior y crear uno nuevo en el canal destino.

## Relación con ownership [#relación-con-ownership]

Transferir de canal y transferir ownership son operaciones distintas. El cambio de canal modifica el contexto al que pertenece el objeto; `owner` determina qué jugador tiene autoridad sobre ese objeto.

No asumas que una transferencia de canal equivale automáticamente a transferir propiedad.

## Referencia a Eco [#referencia-a-eco]

La implementación de `Objeto` expone `channelID`, `id`, `uid`, `hasBeenDestroyed`, `onTransfer` y la lógica relacionada con el ciclo de vida del objeto. La documentación debe considerarse subordinada al comportamiento del repositorio actual.

## Referencia a TNet [#referencia-a-tnet]

El concepto procede del modelo de objetos por canal de TNet, pero la API utilizada aquí pertenece a Eco. Los ejemplos antiguos de TNet no deben utilizarse como API literal.

<Card title="Objetos" href="/docs/red/v1/modelo/objetos">
  Referencia del ciclo de vida e identidad de los objetos de red.
</Card>

<Card title="Varios canales simultáneos" href="/docs/red/v1/guias/multiples-canales">
  Uso práctico de varios canales desde una misma conexión.
</Card>

<Card title="Transferir la propiedad" href="/docs/red/v1/guias/transferir-propiedad">
  Diferencia entre cambiar de canal y cambiar de propietario.
</Card>

<Card title="Eco en GitHub" href="https://github.com/Nervelink/eco">
  Código fuente de la implementación actual.
</Card>
