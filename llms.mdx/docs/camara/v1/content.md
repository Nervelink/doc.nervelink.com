# Inicio Rápido (/docs/camara/v1)



<Callout title="Qué es Cámara" type="info">
  `Cámara` es un módulo de Pandora Crystal orientado a controlar la posición, rotación, proyección y transición de cámaras, además de proporcionar herramientas para difuminado de obstáculos, depuración y distintos estilos de vista.
</Callout>

El núcleo del módulo gira alrededor de `Camara`: recibe una `VistaCamara`, calcula su posición y dirección, aplica transiciones y suavizado y actualiza las propiedades de la `Camera` de Unity. La implementación también integra un algoritmo de difuminado para generar una `RenderTexture` auxiliar. fileciteturn411file0

## Arquitectura [#arquitectura]

```text
Camara
├── VistaCamara
│   └── Tipo de vista
│       ├── Posición
│       ├── Dirección
│       ├── Proyección
│       └── Dimensiones / FOV
│
├── Movimiento
│   ├── Transición
│   ├── Suavizado de posición
│   └── Suavizado de rotación
│
├── Difuminado
│   ├── Configuración
│   ├── Algoritmo
│   └── RenderTexture auxiliar
│
└── Herramientas
    ├── Debug
    ├── Extensiones
    └── Editor
```

## Capacidades principales [#capacidades-principales]

<Cards>
  <Card title="Vistas" href="/docs/camara/v1/fundamentos/vistas">
    Define dónde mira la cámara, cómo se mueve y qué propiedades de proyección utiliza.
  </Card>

  <Card title="Transiciones y suavizado" href="/docs/camara/v1/fundamentos/transiciones">
    Cambia entre vistas de forma inmediata o interpolada.
  </Card>

  <Card title="Difuminado" href="/docs/camara/v1/fundamentos/difuminado">
    Oculta obstáculos entre la cámara y la escena mediante una textura auxiliar y un algoritmo de difuminado.
  </Card>

  <Card title="Referencia API" href="/docs/camara/v1/referencia">
    Consulta 

    `Camara`

    , algoritmos, configuraciones, estructuras y tipos auxiliares.
  </Card>
</Cards>

## Primer recorrido [#primer-recorrido]

<Steps>
  <Step>
    Configura la cámara de Unity y añade 

    `Camara`

     al mismo GameObject.
  </Step>

  <Step>
    Asigna una 

    `VistaCamara`

     para definir la posición y dirección objetivo.
  </Step>

  <Step>
    Selecciona 

    `Update`

     o 

    `LateUpdate`

     según el orden de actualización que necesite la cámara.
  </Step>

  <Step>
    Elige el tipo de suavizado y ajusta sus parámetros de posición y rotación.
  </Step>

  <Step>
    Activa el difuminado únicamente cuando el juego necesite resolver obstáculos entre cámara y objetivo.
  </Step>
</Steps>

## Versión y dependencias [#versión-y-dependencias]

La versión declarada actualmente por el paquete es **1.1.2.11**. Depende de `Motor 1.2.7.5` y `Núcleo 1.3.7.4`. fileciteturn411file0

## Qué leer después [#qué-leer-después]

[Requisitos](/docs/camara/v1/requisitos) → [Fundamentos](/docs/camara/v1/fundamentos) → [Guías](/docs/camara/v1/guias) → [Referencia](/docs/camara/v1/referencia)
