# AutoUnirse (/docs/red/v1/ejemplos/auto-unirse)



# AutoUnirse [#autounirse]

`AutoUnirse` es un componente de conveniencia para proyectos que deben conectar automáticamente al servidor y entrar en un canal al arrancar.

## Configuración [#configuración]

```csharp
public string serverAddress = "127.0.0.1";
public int serverPort = 5127;
public string firstLevel = "Example 1";
public int channelID = 1;
public bool persistent = false;
public bool allowUDP = true;
public bool connectOnStart = true;
```

El componente mantiene su `GameObject` entre escenas para que la conexión pueda sobrevivir al cambio de nivel.

## Flujo [#flujo]

<Steps>
  <Step>
    ### Arranque [#arranque]

    Si `connectOnStart` está habilitado, `AutoUnirse` llama a `Connect()`.
  </Step>

  <Step>
    ### Conexión [#conexión]

    `Connect()` utiliza `Eco.Conectar(serverAddress, serverPort)`.
  </Step>

  <Step>
    ### UDP [#udp]

    Si `allowUDP` está activo, solicita un puerto UDP aleatorio dentro del rango configurado por el ejemplo.
  </Step>

  <Step>
    ### Canal [#canal]

    Tras `Eco.alConectar`, el ejemplo llama a `Eco.UnirseACanal(...)`.
  </Step>
</Steps>

## Cuándo utilizarlo [#cuándo-utilizarlo]

Es apropiado para:

* una escena de arranque de servidor;
* herramientas internas;
* prototipos multijugador;
* juegos donde no existe menú de selección de servidor.

Para juegos con matchmaking o múltiples destinos, es preferible implementar el flujo directamente sobre `Eco`.

<Callout title="No ocultar el estado de conexión" type="info">
  `AutoUnirse` simplifica el workflow, pero la UI del juego debe seguir reaccionando a `Eco.alConectar`, `Eco.alDesconectar` y `Eco.alUnirseACanal`.
</Callout>

## Fuente [#fuente]

<Card title="AutoUnirse.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/AutoUnirse.cs">
  Implementación actual del helper.
</Card>
