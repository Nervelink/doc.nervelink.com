# AutoSincronizar (/docs/red/v1/ejemplos/auto-sincronizar)



# AutoSincronizar [#autosincronizar]

`AutoSincronizar` sincroniza automáticamente una lista de campos o propiedades de un componente asociado a un `Objeto`.

Es una herramienta de conveniencia y prototipado, no un sustituto universal de una arquitectura de sincronización específica.

## Configuración [#configuración]

Cada entrada contiene:

```text
Component target
string propertyName
```

El componente localiza el campo o propiedad mediante reflexión al iniciar.

```csharp
updatesPerSecond = 10f;
isSavedOnServer = true;
isImportant = true;
onlyOwnerCanSync = true;
```

## Modelo de actualización [#modelo-de-actualización]

```text
Campo/propiedad Unity
        ↓
comparar con último valor
        ↓
¿cambió?
   ├── no → no enviar
   └── sí → sincronizar
```

El sistema no envía actualizaciones cuando los valores no han cambiado.

## Frecuencia [#frecuencia]

`updatesPerSecond` limita la frecuencia de comprobación.

Con `0`, el bucle periódico no envía actualizaciones; los valores se pueden entregar a jugadores nuevos mediante el mecanismo específico de entrada.

## Fiabilidad [#fiabilidad]

`isImportant` determina el camino de transporte utilizado:

```text
Importante     → Send
No importante  → SendQuickly
```

Usa el segundo caso para datos frecuentes donde perder valores intermedios sea aceptable.

## Autoridad [#autoridad]

`onlyOwnerCanSync` limita el envío al propietario del objeto.

```csharp
onlyOwnerCanSync = true;
```

Es una buena configuración por defecto para evitar que varios clientes intenten publicar el mismo estado.

## Persistencia [#persistencia]

`isSavedOnServer` determina si la actualización se guarda como estado del servidor. Si está desactivado, el componente registra un callback de entrada y envía el estado al jugador nuevo directamente.

<Callout title="Rendimiento" type="warn">
  `AutoSincronizar` usa reflexión para leer y escribir las propiedades. Para unidades con actualización frecuente, define una sincronización especializada en lugar de aumentar indiscriminadamente `updatesPerSecond`.
</Callout>

## Fuente [#fuente]

<Card title="AutoSincronizar.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/AutoSincronizar.cs">
  Implementación actual.
</Card>

<Card title="Sincronización" href="/docs/red/v1/comunicacion/sincronizacion">
  Modelo de sincronización de estado de Eco.
</Card>
