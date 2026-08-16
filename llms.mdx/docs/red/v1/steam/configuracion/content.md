# Configuración (/docs/red/v1/steam/configuracion)



La integración de Eco debe ejecutarse sólo cuando la plataforma lo permita. El código actual utiliza compilación condicionada para excluir Steam en plataformas no compatibles y en determinados modos de ejecución. fileciteturn190file0

## App ID [#app-id]

Configura el App ID correspondiente al juego en la clase de integración. El valor no debe confundirse con el Steam ID de un usuario.

```text
App ID       → identifica el juego
Steam ID     → identifica al usuario
```

## Inicialización [#inicialización]

Durante el arranque, Eco comprueba la integridad de Steamworks y ejecuta la inicialización de `SteamAPI`. Si Steam no está disponible, la integración se desactiva en lugar de asumir que la plataforma está activa. fileciteturn190file0

## Callbacks [#callbacks]

La API de Steam requiere procesar callbacks. La integración de Eco los ejecuta periódicamente y aumenta su atención cuando existe actividad de red P2P. fileciteturn190file0

## Editor y builds [#editor-y-builds]

No diseñes el flujo suponiendo que Steam siempre está activo. Debe existir una ruta de ejecución sin Steam para desarrollo local, pruebas y plataformas donde la integración no se compile.

<Callout title="Recomendación" type="warn">
  Prueba siempre una sesión local sin Steam antes de diagnosticar un problema de red como problema de Steam. Primero valida Eco; después añade la capa de plataforma.
</Callout>
