# Inicio Rápido (/docs/misiones/v1)



<Callout title="Estado actual del módulo" type="info">
  `Misiones` es actualmente un módulo de datos que prepara la infraestructura para almacenar y cargar la base de datos de misiones. El manifiesto del paquete define como objetivo una misión principal, misiones secundarias, ramificaciones y resultados distintos, pero esas reglas de ejecución no están implementadas en el código disponible actualmente.
</Callout>

## Arquitectura [#arquitectura]

```text
Misiones
│
├── DatosMisiones
│   └── Base de datos de misiones
│
├── Assets / Resources
│   └── Datos/DatosMisiones
│
└── Integración con Motor
    └── IDatos
```

`DatosMisiones` hereda de `IDatos` y utiliza el sistema de datos de `Pandora.Motor` para cargar una copia de la base de datos o inicializarla cuando no existe. fileciteturn453file0

<Cards>
  <Card title="Fundamentos" href="/docs/misiones/v1/fundamentos">
    Modelo de datos, responsabilidades y ciclo de carga.
  </Card>

  <Card title="Guías" href="/docs/misiones/v1/guias">
    Workflows para integrar y preparar la base de datos.
  </Card>

  <Card title="Referencia" href="/docs/misiones/v1/referencia">
    API y tipos disponibles actualmente.
  </Card>
</Cards>

## Primer recorrido [#primer-recorrido]

<Steps>
  <Step>
    Instala el módulo dentro del proyecto Pandora y asegúrate de tener disponible `Pandora.Motor`.
  </Step>

  <Step>
    Verifica que exista la base de datos de misiones en la ruta de recursos esperada.
  </Step>

  <Step>
    Accede a `DatosMisiones.I` cuando necesites la instancia cargada desde `Resources`.
  </Step>

  <Step>
    Usa `DatosMisiones.Cargar()` cuando necesites obtener una copia de la base de datos mediante el mecanismo de `IDatos`.
  </Step>
</Steps>

## Dependencia [#dependencia]

El manifiesto actual declara `com.pandora.modulo.motor` versión **1.1.4.0** como dependencia del paquete `Misiones 1.0.1.1`. fileciteturn451file0

## Qué leer después [#qué-leer-después]

[Requisitos](/docs/misiones/v1/requisitos) → [Fundamentos](/docs/misiones/v1/fundamentos) → [Guías](/docs/misiones/v1/guias) → [Referencia](/docs/misiones/v1/referencia)
