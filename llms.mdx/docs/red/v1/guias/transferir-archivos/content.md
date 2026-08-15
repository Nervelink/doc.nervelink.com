# Transferir archivos (/docs/red/v1/guias/transferir-archivos)



# Transferir archivos [#transferir-archivos]

Eco incluye operaciones de cliente para trabajar con archivos asociados al servidor. Estas operaciones son diferentes de la sincronización de estado: aquí se solicita contenido o información de archivos, mientras que `Nodo`, RFC y objetos representan el estado de la sesión.

## Obtener una lista de archivos [#obtener-una-lista-de-archivos]

El cliente mantiene callbacks asociados a cada solicitud de listado. Conceptualmente:

```csharp
cliente.GetFileList("datos", (ruta, archivos) =>
{
    foreach (var archivo in archivos)
    {
        Debug.Log(archivo);
    }
});
```

El callback recibe la ruta consultada y la lista de archivos encontrada.

## Cargar un archivo [#cargar-un-archivo]

La carga de un archivo devuelve sus datos como bytes:

```csharp
cliente.LoadFile("datos/config.bin", (nombre, datos) =>
{
    // Procesar datos.
});
```

El sistema mantiene callbacks separados para cada llamada de carga, de forma que varias solicitudes pueden convivir sin sobrescribirse entre sí.

## Cuándo utilizar archivos [#cuándo-utilizar-archivos]

Los archivos son apropiados para contenido que necesite llegar desde el servidor sin convertirlo en estado sincronizado de un `Objeto`.

| Necesidad                  | Mecanismo           |
| -------------------------- | ------------------- |
| Propiedad de una entidad   | `Nodo` / `Set`      |
| Acción puntual             | RFC                 |
| Estado compartido          | Sincronización      |
| Descargar contenido        | Carga de archivos   |
| Saber qué archivos existen | Listado de archivos |

## Precauciones [#precauciones]

No utilices el sistema de archivos como sustituto de la sincronización de gameplay. Descargar periódicamente un archivo para replicar una propiedad suele ser una arquitectura peor que modelar esa propiedad como estado de red.

Tampoco debes asumir que todos los clientes tienen acceso arbitrario al sistema de archivos del servidor. El contenido disponible depende de la configuración y de las reglas del servidor.

## Referencias [#referencias]

<Card title="Transporte" href="/docs/red/v1/transporte">
  Capas utilizadas para transportar solicitudes y respuestas.
</Card>

<Card title="Serialización" href="/docs/red/v1/transporte/serializacion">
  Cómo se representan los datos que viajan por la red.
</Card>
