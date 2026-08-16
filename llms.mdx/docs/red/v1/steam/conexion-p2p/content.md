# Conexión P2P (/docs/red/v1/steam/conexion-p2p)



Eco implementa una conexión personalizada sobre Steam P2P mediante `IConnection`. La clase interna `P2PConnection` convierte los buffers de Eco en paquetes enviados al Steam ID remoto. fileciteturn190file0

## Flujo [#flujo]

```text
Cliente A
   │
   │ paquete Eco
   ▼
P2PConnection
   │
   │ Steam Networking
   ▼
Steam
   │
   ▼
Steam del jugador B
   │
   ▼
P2PConnection
   │
   ▼
Eco
```

El gameplay no necesita cambiar su protocolo sólo porque el transporte sea Steam.

## Conectar manualmente [#conectar-manualmente]

La integración expone `Steam.Conectar(string)` para iniciar una conexión P2P mediante el Steam ID del usuario remoto. El README original de la integración utiliza un Steam ID numérico como ejemplo. fileciteturn189file0

```text
Steam.Conectar("76561198012345678")
```

Si la cadena no representa un Steam ID válido, la integración no intenta tratarla como una conexión P2P y Eco puede continuar con su flujo normal.

## Sesiones [#sesiones]

La integración registra conexiones abiertas y cerradas y transforma la desconexión P2P en un estado de conexión que Eco puede procesar. fileciteturn190file0

<Callout title="P2P no significa gameplay P2P" type="warn">
  El transporte puede ser P2P, pero tu juego puede seguir siendo servidor-autoritativo. P2P describe el camino de comunicación, no quién decide el resultado de la partida.
</Callout>
