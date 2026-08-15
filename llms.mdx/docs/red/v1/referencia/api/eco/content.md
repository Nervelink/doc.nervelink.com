# Eco (/docs/red/v1/referencia/api/eco)



# Eco [#eco]

`Eco` es la fachada principal del sistema de red. Se inicializa como sistema de Pandora y expone métodos estáticos para que el código de gameplay pueda trabajar con la red sin acceder directamente a `ClienteJuego`.

<Callout title="No instanciar Eco" type="warn">
  La instancia del sistema es gestionada por Pandora/Alexandria. Desde el juego utiliza la API estática de `Eco` y reserva `Eco.Cliente` para necesidades avanzadas.
</Callout>

## Estado de conexión [#estado-de-conexión]

```csharp
bool conectado = Eco.EstaConectado;
bool intentando = Eco.IntentandoConectar;
bool enAlgunCanal = Eco.EstaEnAlgunCanal;
int idJugador = Eco.IdJugador;
int ping = Eco.Ping;
```

Propiedades especialmente útiles:

| Propiedad            | Significado                               |
| -------------------- | ----------------------------------------- |
| `EstaConectado`      | Existe una conexión activa.               |
| `IntentandoConectar` | Hay un intento de conexión en curso.      |
| `EstaEnAlgunCanal`   | El jugador pertenece a al menos un canal. |
| `Canales`            | Canales activos para el jugador.          |
| `Jugador`            | Jugador local.                            |
| `IdJugador`          | Identificador asignado por el servidor.   |
| `NombreJugador`      | Nombre visible del jugador.               |
| `Ping`               | Latencia actual registrada.               |
| `PuedeUsarUDP`       | UDP está disponible para el cliente.      |

## Conexión [#conexión]

```csharp
Eco.Conectar("192.168.1.20", 5127);
Eco.Desconectar();
Eco.Desconectar(1f);
```

También existe `Eco.Conectar()` para conectar al servidor local iniciado por Eco/`Servidor`.

<Steps>
  <Step>
    ### Preparar nombre y datos [#preparar-nombre-y-datos]

    Establece `Eco.NombreJugador` y, si procede, los datos iniciales del jugador antes de conectar.
  </Step>

  <Step>
    ### Conectar [#conectar]

    Llama a `Eco.Conectar(...)` y espera `Eco.alConectar`.
  </Step>

  <Step>
    ### Entrar en un canal [#entrar-en-un-canal]

    Utiliza `Eco.UnirseACanal(...)`, `Eco.CrearCanal(...)` o `Eco.UnirseACanalAleatorio(...)`.
  </Step>

  <Step>
    ### Operar sobre la sesión [#operar-sobre-la-sesión]

    Consulta canales, crea objetos, sincroniza estado o envía RFC.
  </Step>
</Steps>

## Canales [#canales]

```csharp
Eco.UnirseACanal(10, "Mundo", true, 100, null);
Eco.UnirseACanalAleatorio("Arena", false, 4, null);
Eco.CrearCanal("Arena", false, 4, null);
Eco.SalirDeCanal(10);
Eco.SalirDeTodosLosCanales();
```

La API permite administrar también el estado del canal:

```csharp
Eco.CerrarCanal(10);
Eco.BloquearCanal(10, true);
Eco.EstablecerLimiteJugadores(10, 8);
Eco.CargarEscena(10, "Arena");
```

## Datos de servidor, canal y jugador [#datos-de-servidor-canal-y-jugador]

Eco mantiene tres ámbitos de datos además de los datos propios de cada `Objeto`:

```text
Servidor
└── DatosServidor

Canal
└── DatosCanal

Jugador
└── DatosJugador
```

Ejemplos:

```csharp
Eco.EstablecerDatosJugador("Perfil/Nivel", 25);
int nivel = Eco.ObtenerDatosJugador<int>("Perfil/Nivel", 1);

Eco.EstablecerDatosCanal(10, "Reglas/Modo", "Arena");
string modo = Eco.ObtenerDatosCanal<string>(10, "Reglas/Modo", "Normal");

int maxJugadores = Eco.ObtenerDatosServidor<int>("MaxJugadores", 64);
```

Los datos de servidor requieren privilegios de administrador para modificarse.

## Objetos de red [#objetos-de-red]

```csharp
Eco.Instanciar(10, "CreateAtPosition", "Player", false, posicion, rotacion);
```

También existen operaciones de exportación/importación:

```csharp
Eco.ExportarObjetos(objetos, datos => { /* ... */ });
Eco.ImportarObjetos(10, datos, ids => { /* ... */ });
```

## Transporte y diagnóstico [#transporte-y-diagnóstico]

```csharp
Eco.IniciarUDP(12000);
Eco.PuedeUsarUDP;
Eco.PaquetesEnviados;
Eco.PaquetesRecibidos;
Eco.BytesEnviados;
Eco.BytesRecibidos;
Eco.DimensionPaqueteEntrada;
Eco.DimensionPaqueteDisponible;
```

Para diagnóstico avanzado también están `Eco.PuntoTcp`, `Eco.JugadorOrigenPaquete` e `Eco.IdOrigenPaquete`.

## Paquetes personalizados [#paquetes-personalizados]

```csharp
Eco.EstablecerControlPaquete(Paquete.RequestPing, MiControlador);
```

También puede registrarse un ID de byte propio para extensiones de protocolo.

## Archivos [#archivos]

```csharp
Eco.GuardarArchivo("Partidas/slot1.dat", datos);
Eco.CargarArchivo("Partidas/slot1.dat", (nombre, contenido) => { /* ... */ });
Eco.EliminarArchivo("Partidas/slot1.dat");
```

Existe además una caché local asociada al servidor mediante `EscribirCache` y `LeerCache`.

## Coordinación [#coordinación]

Para confirmar que las operaciones enviadas anteriormente han llegado al servidor:

```csharp
Eco.EnviarConRetorno(() =>
{
    Debug.Log("El servidor ha procesado el callback");
});
```

Y en una corrutina:

```csharp
yield return Eco.EsperarRetorno();
```

## Eventos principales [#eventos-principales]

Eco expone eventos para conexión, desconexión, canales, jugadores y datos:

```csharp
Eco.alConectar += OnConectar;
Eco.alDesconectar += OnDesconectar;
Eco.alUnirseACanal += OnCanal;
Eco.alEntrarJugador += OnJugador;
Eco.alCambiarDatosJugador += OnDatosJugador;
```

Suscríbete normalmente en `OnEnable` y desuscríbete en `OnDisable`.

## Código fuente [#código-fuente]

<Card title="Eco.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Sistemas/Eco.cs">
  Implementación de la fachada principal.
</Card>

<Card title="ClienteJuego.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/ClienteJuego.cs">
  Implementación del cliente interno utilizado por Eco.
</Card>
