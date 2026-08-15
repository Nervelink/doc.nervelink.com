# Eco (/docs/red/v1/referencia/api/eco)



# Eco [#eco]

`Eco` es la fachada principal del sistema de red. Se inicializa como sistema de Pandora y concentra las operaciones que el código de juego necesita para trabajar con la sesión sin acceder directamente a los detalles del runtime.

<Callout title="Regla de uso" type="info">
  En gameplay utiliza `Eco`. Accede a `Eco.Cliente`, `Servidor` o `ServidorJuego` cuando necesites diagnóstico, integración o infraestructura avanzada.
</Callout>

## Estado [#estado]

| Miembro                  | Resultado                             |
| ------------------------ | ------------------------------------- |
| `Eco.EstaConectado`      | `bool`, conexión activa               |
| `Eco.IntentandoConectar` | `bool`, intento de conexión           |
| `Eco.EstaEnAlgunCanal`   | `bool`, pertenece a uno o más canales |
| `Eco.IdJugador`          | `int`, ID del jugador local           |
| `Eco.NombreJugador`      | `string`, nombre actual               |
| `Eco.Ping`               | latencia observada                    |
| `Eco.Jugador`            | `Jugador`, participante local         |
| `Eco.Canales`            | colección de canales activos          |

## Conexión [#conexión]

Firmas de uso habitual:

```csharp
Eco.Conectar(string direccion, int puerto = 5127);
Eco.Conectar();
Eco.Desconectar();
Eco.Desconectar(float retraso);
```

`Eco.Conectar()` se utiliza cuando la sesión local ya tiene disponible el servidor correspondiente.

## Canales [#canales]

```csharp
Eco.UnirseACanal(int idCanal, string nombreNivel, bool persistente, int limiteJugadores, string contraseña);
Eco.SalirDeCanal(int idCanal);
Eco.SalirDeTodosLosCanales();
Eco.ObtenerCanal(int idCanal);
Eco.EstaEnCanal(int idCanal);
```

Para crear o buscar una partida existen además las operaciones de creación y selección aleatoria de canal.

<Callout title="Multicanal" type="warning">
  No utilices `idUltimoCanal` como sustituto de `idCanal` en una arquitectura multicanal. Pasa siempre el contexto de canal a las operaciones que lo necesiten.
</Callout>

## Datos [#datos]

Eco dispone de tres ámbitos de datos antes de llegar a los datos de `Objeto`:

```text
Servidor → datos globales
Canal    → datos del ámbito de partida
Jugador  → datos del participante
```

Ejemplo:

```csharp
Eco.EstablecerDatosJugador("Perfil/Nivel", 25);
int nivel = Eco.ObtenerDatosJugador<int>("Perfil/Nivel", 1);

Eco.EstablecerDatosCanal(10, "Reglas/Modo", "Arena");
string modo = Eco.ObtenerDatosCanal<string>(10, "Reglas/Modo", "Normal");
```

Los datos globales del servidor están sujetos a autorización cuando se modifican.

## Objetos [#objetos]

```csharp
Eco.Instanciar(
    10,
    "CreateAtPosition",
    "Player",
    false,
    posicion,
    rotacion
);
```

Para operaciones persistentes existen además exportación e importación de objetos.

## Retorno y coordinación [#retorno-y-coordinación]

```csharp
Eco.EnviarConRetorno(() =>
{
    // Procesamiento remoto anterior ha alcanzado su retorno.
});

yield return Eco.EsperarRetorno();
```

Utiliza estos mecanismos para coordinar operaciones concretas, no para reemplazar la sincronización de estado.

## Diagnóstico [#diagnóstico]

```csharp
Eco.Ping;
Eco.PaquetesEnviados;
Eco.PaquetesRecibidos;
Eco.BytesEnviados;
Eco.BytesRecibidos;
Eco.DimensionPaqueteEntrada;
Eco.DimensionPaqueteDisponible;
```

Son especialmente útiles para construir una pantalla de diagnóstico o registrar telemetría de red.

## Archivos y caché [#archivos-y-caché]

Eco también expone operaciones para el servicio de archivos y caché asociada al servidor. Son independientes de la persistencia lógica de objetos.

## Eventos [#eventos]

Los eventos de conexión, canales, jugadores y datos permiten reaccionar sin consultar cada frame:

```csharp
Eco.alConectar += OnConectar;
Eco.alDesconectar += OnDesconectar;
Eco.alUnirseACanal += OnCanal;
Eco.alEntrarJugador += OnJugador;
Eco.alCambiarDatosJugador += OnDatosJugador;
```

Suscríbete y desuscríbete en el ciclo de vida del sistema que consume los eventos.

## Integración avanzada [#integración-avanzada]

`Eco.Cliente` expone `ClienteJuego` para acceder a estado interno de sesión. Los paquetes personalizados y `IConnection` son mecanismos de infraestructura y deben documentarse junto con transporte y protocolo.

## Fuente [#fuente]

<Card title="Eco.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Sistemas/Eco.cs">
  Implementación de la fachada principal.
</Card>

<Card title="ClienteJuego.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/ClienteJuego.cs">
  Runtime cliente utilizado internamente por Eco.
</Card>

<Card title="Inicio rápido" href="/docs/red/v1">
  Ruta recomendada para la primera integración.
</Card>
