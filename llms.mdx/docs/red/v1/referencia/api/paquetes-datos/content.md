# Paquetes de datos (/docs/red/v1/referencia/api/paquetes-datos)



# Paquetes de datos [#paquetes-de-datos]

Eco separa los datos compartidos en tres ámbitos: servidor, canal y jugador. Los paquetes correspondientes transportan modificaciones y notificaciones de esos ámbitos.

```text
Servidor
 └── DatosServidor

Canal
 └── DatosCanal

Jugador
 └── DatosJugador
```

## Datos de servidor [#datos-de-servidor]

Los datos globales representan configuración o estado que no pertenece a un canal concreto.

El acceso de modificación está restringido en función de los permisos del servidor.

## Datos de canal [#datos-de-canal]

Los datos de canal se identifican mediante:

```text
channelID + ruta + valor
```

Por ejemplo:

```csharp
Eco.EstablecerDatosCanal(10, "Reglas/Modo", "Arena");
```

El protocolo correspondiente debe indicar explícitamente el canal para evitar ambigüedad cuando una conexión participa en varios canales.

## Datos de jugador [#datos-de-jugador]

Los datos del jugador siguen el mismo modelo jerárquico, pero pertenecen a un participante concreto.

```csharp
Eco.EstablecerDatosJugador("Perfil/Nivel", 20);
```

El receptor identifica al jugador mediante su `playerID`.

## Persistencia [#persistencia]

Los datos compartidos pueden persistir con el ámbito al que pertenecen, pero esto no significa que todos los datos se guarden con la misma estrategia. Canal, jugador y estado del servidor tienen ciclos de vida distintos.

<Callout title="Multicanal" type="info">
  Cuando una conexión está en varios canales, siempre documenta el `channelID` junto al dato de canal. No dependas del último canal utilizado como contexto implícito.
</Callout>

## Diagnóstico [#diagnóstico]

Cuando un cambio de datos no aparece en otro cliente, comprueba:

1. ámbito del dato;
2. ID de canal o jugador;
3. ruta dentro del `Nodo`;
4. autoridad del emisor;
5. paquete correspondiente;
6. persistencia, si el valor debe llegar a jugadores futuros.

## Referencias [#referencias]

<Card title="Jugador" href="/docs/red/v1/referencia/api/jugador">
  Identidad y datos de un participante.
</Card>

<Card title="Canal" href="/docs/red/v1/referencia/api/canal">
  Estado y datos por canal.
</Card>

<Card title="Nodo" href="/docs/red/v1/referencia/api/nodo">
  Estructura jerárquica de los datos.
</Card>
