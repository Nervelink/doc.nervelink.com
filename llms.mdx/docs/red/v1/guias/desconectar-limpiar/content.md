# Desconectar y limpiar la sesión (/docs/red/v1/guias/desconectar-limpiar)



# Desconectar y limpiar la sesión [#desconectar-y-limpiar-la-sesión]

Una desconexión no debería tratarse únicamente como cerrar un socket. El cliente mantiene jugadores, canales, objetos, colas y estado de conexión que deben quedar en un estado coherente antes de volver a conectarse.

## Desconectar [#desconectar]

La operación de desconexión pertenece al cliente de red. Después de solicitarla, no asumas que el estado remoto desaparece de forma instantánea en el mismo frame.

```text
Conectado
   ↓
Solicitar desconexión
   ↓
Cerrar transporte
   ↓
Limpiar sesión
   ↓
Desconectado
```

## Canales y objetos [#canales-y-objetos]

Durante la desconexión los canales dejan de representar una sesión válida y los objetos asociados a ellos dejan de ser utilizables para enviar mensajes.

Una comprobación especialmente útil es `Objeto.PuedeEnviar`:

```csharp
if (objeto.PuedeEnviar)
{
    // Operación de red válida.
}
```

No conserves referencias a objetos de red de una sesión anterior para utilizarlas después de reconectar sin comprobar su registro y contexto.

## Reconexion [#reconexion]

Una nueva conexión debe reconstruir la sesión. No es correcto asumir que un `playerID`, `channelID`, `uid` o colección de objetos de la sesión anterior seguirá siendo válido.

```text
Sesión A
  └── conexión + canales + objetos
             ↓
         desconexión
             ↓
Sesión B
  └── nueva conexión + nuevo estado
```

El hecho de que el mismo dispositivo o usuario vuelva a conectarse no convierte las dos sesiones de red en la misma sesión técnica.

## Errores habituales [#errores-habituales]

### Enviar durante la desconexión [#enviar-durante-la-desconexión]

Comprueba el estado de conexión y la posibilidad real de envío antes de lanzar RFC o cambios de estado.

### Reutilizar objetos destruidos [#reutilizar-objetos-destruidos]

Un objeto marcado como destruido o que ya no pertenece a un canal válido no debe recibir nuevas operaciones de red.

### Mezclar datos de dos sesiones [#mezclar-datos-de-dos-sesiones]

Limpia listeners, referencias y caches propios del juego cuando corresponda. Eco gestiona su estado de red, pero el juego sigue siendo responsable de sus propias referencias externas.

## Referencias [#referencias]

<Card title="Cliente" href="/docs/red/v1/runtime/cliente">
  Estados de conexión y funcionamiento del cliente.
</Card>

<Card title="Objetos" href="/docs/red/v1/modelo/objetos">
  Ciclo de vida y posibilidad de envío de los objetos de red.
</Card>
