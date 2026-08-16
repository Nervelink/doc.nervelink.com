# Diagnóstico (/docs/red/v1/steam/diagnostico)



Diagnostica por capas.

```text
¿Steam está activo?
 ↓
¿Steamworks inicializó correctamente?
 ↓
¿Steam ID válido?
 ↓
¿Callbacks se procesan?
 ↓
¿Existe sesión P2P?
 ↓
¿IConnection está activo?
 ↓
¿Eco está conectado?
 ↓
¿Jugador entra en el canal?
 ↓
¿Gameplay funciona?
```

## Steam no inicializa [#steam-no-inicializa]

Comprueba `Packsize.Test()`, `DllCheck.Test()` y el resultado de `SteamAPI.Init()`. La implementación actual detiene la integración si las comprobaciones fallan. fileciteturn190file0

## El amigo no puede unirse [#el-amigo-no-puede-unirse]

Comprueba:

1. Steam está activo en ambos procesos.
2. El anfitrión está realmente escuchando.
3. `PermitirUnirseAmigos(true)` está activo.
4. Rich Presence contiene `connect`.
5. Steam arranca el juego con `+connect`.
6. `ArgumentoAutoConectar` encuentra el valor.
7. `Steam.Conectar` acepta el Steam ID.
8. Eco recibe la conexión.

## Conecta Steam pero no entra al juego [#conecta-steam-pero-no-entra-al-juego]

Eso indica que la capa Steam puede estar funcionando y el problema estar después:

```text
Steam P2P ✓
      ↓
Eco connection ?
      ↓
Canal ?
      ↓
Jugador ?
      ↓
Objetos ?
```

No depures el transporte cuando el fallo ya está en el estado de Eco.

## Problemas intermitentes [#problemas-intermitentes]

Registra Steam ID remoto, estado de la conexión, canal, objeto y tipo de paquete. La integración dispone de estructuras para conexiones abiertas y cerradas que pueden ayudar a separar un fallo P2P de un fallo de gameplay. fileciteturn190file0
