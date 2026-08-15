# APIs de bajo nivel (/docs/red/v1/referencia/api/bajo-nivel)



# APIs de bajo nivel [#apis-de-bajo-nivel]

Estas APIs permiten trabajar por debajo de `Eco`, `Objeto` y `Componente`. Son útiles para extensiones del protocolo, herramientas de diagnóstico y transportes personalizados.

<Callout title="API de infraestructura" type="warn">
  Empieza siempre por la API de alto nivel. Manipular `Paquete` y `Buffer` implica asumir manualmente responsabilidades que Eco normalmente resuelve por ti.
</Callout>

## Capas [#capas]

```text
Gameplay
   ↓
Eco / Objeto / Componente
   ↓
Paquete
   ↓
Buffer
   ↓
ClienteJuego / Servidor
   ↓
TCP / UDP / IConnection
```

## Paquete [#paquete]

`Paquete` representa una operación del protocolo. Los tipos internos cubren conexión, canales, objetos, datos, archivos, RFC y transporte.

Cuando necesitas una extensión de protocolo, registra un controlador para un identificador que no interfiera con los paquetes utilizados por Eco.

```csharp
Eco.EstablecerControlPaquete(miID, (cliente, buffer) =>
{
    // Leer y procesar el paquete personalizado.
});
```

La lógica del controlador debe consumir el `Buffer` exactamente en el mismo orden en que fue escrito por el emisor.

## Buffer [#buffer]

`Buffer` es la capa de lectura y escritura binaria.

```csharp
Buffer buffer = new Buffer();
buffer.Write(42);
buffer.Write("Eco");

int numero = buffer.ReadInt();
string texto = buffer.ReadString();
```

No confundas el `Buffer` de protocolo con almacenamiento persistente. Es una representación temporal de datos transportables.

## RFC y retorno [#rfc-y-retorno]

Eco conserva mecanismos para esperar a que operaciones remotas anteriores hayan sido procesadas:

```csharp
Eco.EnviarConRetorno(() =>
{
    // El procesamiento anterior ha alcanzado el retorno.
});
```

Y en workflows basados en coroutine:

```csharp
yield return Eco.EsperarRetorno();
```

Úsalo como punto de coordinación; no como mecanismo de sincronización de gameplay continuo.

## Caché y archivos [#caché-y-archivos]

La caché y la API de archivos son capas distintas de la persistencia de objetos:

```text
Nodo / datos       → estado estructurado
Persistencia       → mundo y objetos
Archivos           → almacenamiento de ficheros
Caché              → datos auxiliares asociados al servidor
```

## Tiempo y diagnóstico [#tiempo-y-diagnóstico]

Para mediciones de red utiliza el tiempo de servidor y las métricas expuestas por Eco:

```csharp
double tiempo = Eco.TiempoServidor;
int ping = Eco.Ping;
long bytesEnviados = Eco.BytesEnviados;
long bytesRecibidos = Eco.BytesRecibidos;
```

Estas métricas sirven para instrumentación y diagnóstico sin inspeccionar manualmente los sockets.

## Conexiones personalizadas [#conexiones-personalizadas]

Una conexión personalizada permite conservar el modelo de Eco mientras se sustituye la capa física de transporte. El punto de extensión es `IConnection` y se utiliza en integraciones como Steam Networking.

<Card title="Transportes personalizados" href="/docs/red/v1/avanzado/transportes-personalizados">
  Arquitectura de `IConnection` y sustitución del transporte.
</Card>

## Cuándo bajar de nivel [#cuándo-bajar-de-nivel]

| Necesidad         | API recomendada                |
| ----------------- | ------------------------------ |
| Gameplay          | `Eco`, `Objeto`, `Componente`  |
| Estado            | `Set` / `Get` / sincronización |
| Acción remota     | RFC                            |
| Crear objeto      | `Eco.Instanciar`               |
| Diagnóstico       | métricas de `Eco`              |
| Protocolo propio  | `Paquete` + `Buffer`           |
| Transporte propio | `IConnection`                  |

## Código fuente [#código-fuente]

<Card title="ClienteJuego.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/ClienteJuego.cs">
  Procesamiento de paquetes en el cliente.
</Card>

<Card title="ServidorJuego.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Servidor/ServidorJuego.cs">
  Procesamiento y gestión de red en el servidor.
</Card>
