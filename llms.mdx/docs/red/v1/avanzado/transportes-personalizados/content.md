# Transportes personalizados (/docs/red/v1/avanzado/transportes-personalizados)



# Transportes personalizados [#transportes-personalizados]

Eco separa la API de alto nivel de la conexión física mediante `IConnection`. Esta abstracción es una de las ideas de TNet que conviene conservar porque permite sustituir TCP/UDP o integrar plataformas externas sin reescribir `Eco`, `Objeto` y `Componente`.

## Idea general [#idea-general]

```text
Gameplay
   ↓
Eco / Objeto / Componente
   ↓
Paquete / Buffer
   ↓
IConnection
   ↓
Steam Networking / transporte propio / integración externa
```

El gameplay no debería saber si un paquete ha viajado por una implementación concreta.

## Activación [#activación]

Eco expone `ProtocoloPersonalizado` para instalar una implementación de `IConnection`.

```csharp
Eco.ProtocoloPersonalizado = miConexion;
```

La conexión personalizada debe respetar el contrato de Eco y devolver el comportamiento esperado por el cliente. Una implementación incorrecta puede parecer funcional en conexión inicial y fallar posteriormente con canales, paquetes o cierres.

## Cuándo utilizarlo [#cuándo-utilizarlo]

Un transporte personalizado tiene sentido cuando:

* una plataforma exige una API propia de networking;
* Steam Networking debe sustituir el socket tradicional;
* necesitas un relay o servicio externo;
* quieres integrar una capa de transporte corporativa o de consola;
* necesitas controlar cómo se crean y consumen los endpoints.

Para un juego estándar LAN/WAN no conviene introducir esta capa sin necesidad.

## Qué no debe cambiar [#qué-no-debe-cambiar]

Aunque cambies el transporte, estas abstracciones deben seguir funcionando igual:

```text
Canal
Jugador
Objeto
Componente
RFC
Objetivo
Nodo
Paquete
```

Si el gameplay necesita conocer detalles de `IConnection`, probablemente la abstracción está filtrándose demasiado arriba.

## Steam [#steam]

La integración con Steam es un caso particular de transporte/plataforma. Steam puede aportar identidad, matchmaking o comunicación, pero no conviene mezclar automáticamente esos conceptos con el modelo de `Canal` de Eco.

Un diseño limpio separa:

```text
Steam
├── identidad de plataforma
├── descubrimiento / sesión
└── transporte si se utiliza

Eco
├── Jugador
├── Canal
├── Objeto
├── RFC
└── estado sincronizado
```

## Diagnóstico [#diagnóstico]

Cuando una implementación personalizada conecta pero no funciona correctamente, comprueba en este orden:

<Steps>
  <Step>
    ### Conectividad [#conectividad]

    Verifica que la conexión alcanza el estado conectado de Eco.
  </Step>

  <Step>
    ### Paquetes [#paquetes]

    Verifica que un `Buffer` enviado vuelve al receptor sin truncado ni modificación.
  </Step>

  <Step>
    ### Orden [#orden]

    Comprueba que la conexión conserva las garantías esperadas por la parte fiable del protocolo.
  </Step>

  <Step>
    ### Cierre [#cierre]

    Prueba desconexión manual, pérdida de enlace, cambio de escena y cierre de aplicación.
  </Step>
</Steps>

<Callout title="El transporte no debe reinventar Eco" type="warn">
  `IConnection` está pensado para sustituir la capa física de comunicación, no para crear un segundo sistema de canales, objetos o RPC paralelo.
</Callout>

<Card title="Steam y conexiones" href="/docs/red/v1/avanzado/steam-y-conexiones">
  Integración específica con Steam y plataformas externas.
</Card>

<Card title="Arquitectura" href="/docs/red/v1/fundamentos/arquitectura">
  Dónde encaja `IConnection` dentro del sistema.
</Card>
