# Diagnosticar una conexión (/docs/red/v1/guias/diagnosticar-conexion)



# Diagnosticar una conexión [#diagnosticar-una-conexión]

Cuando algo falla en una sesión multijugador, conviene averiguar primero en qué capa se encuentra el problema: conexión, canal, objeto, comunicación o transporte.

## Comprobar la conexión [#comprobar-la-conexión]

Desde `ClienteJuego` puedes comprobar si existe una conexión válida:

```csharp
if (!cliente.isConnected)
{
    // El transporte no está conectado.
}
```

También existe información de intento de conexión, endpoint TCP, ping y uso de UDP.

## Comprobar canales [#comprobar-canales]

No confundas estar conectado con pertenecer a un canal:

```csharp
if (!cliente.isConnected)
    return;

if (!cliente.IsInChannel(canalID))
    return;
```

En un cliente multicanal, consulta siempre el canal concreto que te interesa.

## Comprobar el objeto [#comprobar-el-objeto]

Antes de enviar una operación sobre un `Objeto`, comprueba al menos:

```csharp
if (!objeto.hasBeenRegistered)
    return;

if (objeto.hasBeenDestroyed)
    return;

if (!objeto.PuedeEnviar)
    return;
```

Estas comprobaciones separan objetos todavía no registrados, objetos destruidos y objetos cuyo canal no permite comunicación en ese momento.

## Observar tráfico [#observar-tráfico]

`ClienteJuego` expone contadores de paquetes y bytes enviados y recibidos. Son útiles para detectar situaciones como:

* un cliente conectado que no recibe tráfico;
* un envío excesivamente frecuente;
* una operación que genera más datos de los esperados;
* diferencias entre tráfico TCP y UDP.

```csharp
Debug.Log($"Ping: {cliente.ping}");
Debug.Log($"TX: {cliente.sentPackets} paquetes / {cliente.sentBytes} bytes");
Debug.Log($"RX: {cliente.receivedPackets} paquetes / {cliente.receivedBytes} bytes");
```

## Diagnóstico por capas [#diagnóstico-por-capas]

```text
¿Está conectado?
      │
      ├── No → transporte / conexión
      │
      └── Sí
           ↓
      ¿Está en el canal?
           │
           ├── No → unión / canal
           │
           └── Sí
                ↓
           ¿Objeto registrado?
                │
                ├── No → creación / registro
                │
                └── Sí
                     ↓
                ¿Puede enviar?
                     │
                     ├── No → autoridad / transición / estado
                     │
                     └── Sí → revisar RFC, sincronización y payload
```

## UDP [#udp]

`canUseUDP` indica si el cliente puede utilizar UDP en la sesión actual. Que UDP exista en la implementación no significa que todo mensaje deba utilizarlo.

Para datos críticos, revisa primero qué garantías necesita la operación y qué transporte utiliza realmente.

## Referencias [#referencias]

<Card title="Cliente" href="/docs/red/v1/runtime/cliente">
  Estados y propiedades del cliente de Eco.
</Card>

<Card title="Transporte" href="/docs/red/v1/transporte">
  Paquetes, buffers, serialización y protocolos.
</Card>

<Card title="Requisitos y limitaciones" href="/docs/red/v1/requisitos">
  Restricciones conocidas de la integración.
</Card>
