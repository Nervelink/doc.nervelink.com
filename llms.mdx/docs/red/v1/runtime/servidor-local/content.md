# Servidor local (/docs/red/v1/runtime/servidor-local)



# Servidor local [#servidor-local]

Eco permite ejecutar `ClienteJuego` y `ServidorJuego` dentro del mismo proceso. El flujo lógico de red se conserva, pero el transporte utiliza colas internas en lugar de sockets.

```text
ClienteJuego
    │
    │ Queue<Buffer>
    ▼
ServidorJuego
```

## Uso [#uso]

Este modo resulta útil para pruebas, herramientas, partidas locales y escenarios donde no es necesario abrir puertos de red.

```csharp
cliente.Connect(servidor);
```

## Ventajas [#ventajas]

El modo local permite probar el comportamiento cliente-servidor sin depender de una conexión TCP real y mantiene separadas las responsabilidades de ambos extremos.

## Diferencia con una conexión remota [#diferencia-con-una-conexión-remota]

| Local               | Remoto                         |
| ------------------- | ------------------------------ |
| Mismo proceso       | Procesos normalmente separados |
| Colas internas      | TCP / UDP                      |
| Sin sockets         | Sockets de red                 |
| Mismo modelo lógico | Mismo modelo lógico            |
