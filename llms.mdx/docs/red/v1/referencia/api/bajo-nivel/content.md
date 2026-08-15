# APIs de bajo nivel (/docs/red/v1/referencia/api/bajo-nivel)



# APIs de bajo nivel [#apis-de-bajo-nivel]

La API de bajo nivel existe para integrar herramientas, protocolos propios y optimizaciones. No es la ruta recomendada para gameplay normal.

<Callout title="Empieza por la API de alto nivel" type="warn">
  Usa `Eco`, `Objeto` y `Componente` mientras sea suficiente. Acceder directamente a paquetes y buffers implica asumir responsabilidades que normalmente resuelve Eco.
</Callout>

## Crear y enviar paquetes [#crear-y-enviar-paquetes]

```csharp
Buffer paquete = Eco.CrearPaquete(Paquete.RequestCreateObject);
Eco.EnviarPaquete(paquete);
```

También pueden registrarse handlers para paquetes personalizados:

```csharp
Eco.EstablecerControlPaquete(
    miId,
    (cliente, lector) =>
    {
        // Procesar paquete personalizado.
    });
```

El objetivo es extender el protocolo sin convertir cada operación de gameplay en manipulación manual de bytes.

## Envíos fiables y no fiables [#envíos-fiables-y-no-fiables]

`Eco.EnviarPaquete` permite elegir fiabilidad e inmediatez:

```csharp
Eco.EnviarPaquete(buffer, fiable: true, instantaneo: false);
```

La decisión afecta al transporte, no a las reglas de autoridad.

## Callbacks y retorno [#callbacks-y-retorno]

Eco expone un mecanismo de retorno para saber cuándo una secuencia enviada ha alcanzado el punto de respuesta esperado:

```csharp
Eco.EnviarConRetorno(() =>
{
    // Operaciones anteriores han rebotado.
});
```

También existe `Eco.EsperarRetorno()` para workflows basados en coroutines.

## Caché local [#caché-local]

La fachada ofrece una caché asociada al servidor conectado:

```csharp
Eco.EscribirCache("perfil.dat", datos);
byte[] datos = Eco.LeerCache("perfil.dat");
```

La caché no sustituye al guardado del servidor: es almacenamiento local asociado a la conexión.

## Archivos remotos [#archivos-remotos]

Eco también puede enviar solicitudes para guardar, cargar y eliminar archivos en el servidor. Esto es una capa distinta del sistema de persistencia de objetos.

```text
Datos estructurados → Nodo
Estado de objetos   → Persistencia
Archivos            → API de archivos
Paquetes            → Buffer / protocolo
```

## Tiempo de servidor [#tiempo-de-servidor]

Para sincronizaciones temporales es preferible utilizar el tiempo del servidor:

```csharp
double ahora = Eco.TiempoServidor;
long ahoraMs = Eco.TiempoServidorMS;
```

Esto evita depender directamente del reloj local de cada máquina.

## Conexión personalizada [#conexión-personalizada]

`Eco.ProtocoloPersonalizado` permite sustituir la capa de conexión por una implementación compatible con `IConnection`.

Este punto es el utilizado por integraciones como Steam Networking: el modelo de Eco permanece mientras cambia la vía de transporte.

<Card title="Transportes personalizados" href="/docs/red/v1/avanzado/steam-y-conexiones">
  Integración de conexiones externas.
</Card>

## Información de diagnóstico [#información-de-diagnóstico]

Para herramientas de desarrollo, Eco expone:

```csharp
Eco.Ping;
Eco.PaquetesEnviados;
Eco.PaquetesRecibidos;
Eco.BytesEnviados;
Eco.BytesRecibidos;
Eco.DimensionColaEntrada;
Eco.DimensionPaqueteDisponible;
Eco.DimensionPaqueteEntrada;
```

Estos valores permiten construir métricas de red sin modificar el protocolo.

## Relación con el protocolo [#relación-con-el-protocolo]

```text
Gameplay
   ↓
Eco / Objeto / Componente
   ↓
CrearPaquete
   ↓
Buffer
   ↓
ClienteJuego
   ↓
TCP / UDP / IConnection
```

Los tipos y paquetes internos son una API de infraestructura. Cualquier uso debe asumir que puede requerir más mantenimiento que las APIs de alto nivel.
