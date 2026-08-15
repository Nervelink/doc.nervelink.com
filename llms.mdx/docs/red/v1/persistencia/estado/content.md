# Estado persistente (/docs/red/v1/persistencia/estado)



# Estado persistente [#estado-persistente]

La persistencia registra información de red que debe sobrevivir a determinados cambios de sesión. No significa que cada dato enviado por el juego se escriba inmediatamente en disco.

## Capas de estado [#capas-de-estado]

```text
Servidor
├── serverData
├── Canales
│   ├── datos del canal
│   └── RFC guardadas
└── Objetos persistentes
    └── datos del objeto
```

`ServidorJuego` mantiene un archivo de mundo y funciones de lectura/escritura que pueden sustituirse por otro almacenamiento, como un servicio externo. fileciteturn233file0

## Qué guardar [#qué-guardar]

Guarda aquello que otro proceso o jugador debe poder reconstruir sin depender de eventos que ya ocurrieron.

Ejemplos:

* objetos persistentes;
* datos de canales;
* RFC guardadas necesarias para reconstrucción;
* configuración global del servidor.

## Qué no guardar [#qué-no-guardar]

No conviertas el estado transitorio de cada frame en datos persistentes. Una posición temporal o una métrica de rendimiento normalmente necesita sincronización, interpolación o un guardado específico del juego, no formar parte del mundo persistido por Eco.

## Guardado y sueño [#guardado-y-sueño]

El servidor puede reducir el uso de memoria de canales sin jugadores mediante `Sleep()` y recuperarlos con `Wake()`. Este mecanismo complementa el almacenamiento: libera memoria activa sin perder necesariamente el estado persistente. fileciteturn233file0

## Integración con el guardado del juego [#integración-con-el-guardado-del-juego]

Eco y el juego pueden tener dos capas distintas:

```text
Eco
└── Estado necesario para reconstruir la red

Juego
└── Progreso, inventario, economía, reglas, etc.
```

Evita guardar ambos sistemas en paralelo sin un contrato claro. La red debe poder reconstruirse desde su propio estado sin asumir detalles internos del save del juego.
