# Runtime Code (/docs/red/v1/avanzado/runtime-code)



# Runtime Code [#runtime-code]

Eco incluye una carpeta `RuntimeCode` para compilar y cargar código C# durante la ejecución. Es una capacidad especializada y debe tratarse como una superficie de extensibilidad, no como una forma habitual de implementar gameplay.

## Arquitectura [#arquitectura]

```text
Código fuente
    ↓
CodeCompiler
    ↓
Compilación
    ↓
Assembly dinámico
    ↓
RuntimeBehaviour / driver
    ↓
Juego
```

La solución incluye infraestructura para compilación, carga de assemblies y reporte de errores.

## Cuándo utilizarlo [#cuándo-utilizarlo]

Casos razonables:

* modding;
* herramientas internas;
* contenido definido por usuarios;
* scripts dinámicos que no pueden compilarse con el proyecto principal.

No es una buena opción para sustituir el código normal de un sistema que conoces de antemano.

<Callout title="Seguridad" type="warn">
  Ejecutar C# generado o recibido externamente equivale a ejecutar código con los permisos del proceso. Nunca trates código de mods desconocidos como contenido de datos inocente.
</Callout>

## Componentes [#componentes]

```text
RuntimeCode
├── CodeCompiler
├── CustomDynamicDriver
├── RuntimeBehaviour
└── CustomReportPrinter
```

## Flujo recomendado [#flujo-recomendado]

<Steps>
  <Step>
    ### Validar origen [#validar-origen]

    Comprueba que el código procede de una fuente de confianza.
  </Step>

  <Step>
    ### Compilar [#compilar]

    Utiliza `CodeCompiler` y registra los errores de compilación.
  </Step>

  <Step>
    ### Cargar [#cargar]

    Añade el assembly al runtime mediante el sistema de carga correspondiente.
  </Step>

  <Step>
    ### Crear comportamiento [#crear-comportamiento]

    Utiliza `RuntimeBehaviour` o el driver dinámico para asociar el código a un objeto.
  </Step>
</Steps>

<Card title="RuntimeCode" href="https://github.com/Nervelink/eco/tree/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/RuntimeCode">
  Implementación de la infraestructura de código dinámico.
</Card>
