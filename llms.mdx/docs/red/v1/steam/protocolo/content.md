# Protocolo (/docs/red/v1/steam/protocolo)



La integración Steam no reemplaza el protocolo de Eco. Implementa un transporte que satisface `IConnection`.

```text
Paquete Eco
   ↓
Buffer
   ↓
IConnection
   ↓
P2PConnection
   ↓
Steam Networking
```

La clase `P2PConnection` recibe un `Buffer`, extrae paquetes completos y los envía al Steam ID remoto. Al desconectarse, genera el estado de desconexión que Eco puede procesar. fileciteturn190file0

## Ventaja [#ventaja]

El código de gameplay no necesita conocer si un paquete viaja por TCP, UDP o Steam P2P. La capa de transporte mantiene esa decisión aislada.

## Limitación conceptual [#limitación-conceptual]

Cambiar el transporte no elimina los problemas de diseño del protocolo. Tamaño de paquetes, frecuencia, serialización, ownership, destinatarios y autoridad siguen siendo responsabilidad de la arquitectura Eco.

<Callout title="Transporte ≠ protocolo de gameplay" type="info">
  Steam proporciona el camino. Eco define qué se transmite y el juego define qué significa.
</Callout>
