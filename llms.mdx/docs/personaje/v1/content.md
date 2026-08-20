# Inicio Rápido (/docs/personaje/v1)



<Callout title="Qué es Personaje" type="info">
  El módulo Personaje concentra la locomoción de personajes controlados por el jugador y de personajes que siguen rutas, junto con sensores, animación, audio, entrada y herramientas auxiliares.
</Callout>

## Arquitectura en una vista [#arquitectura-en-una-vista]

```text
                         Personaje
                             │
             ┌───────────────┼───────────────┐
             │               │               │
        Movimiento        Sensores       Presentación
             │               │          ┌────┴────┐
      ┌──────┴──────┐        │       Animación  Audio
      │             │        │
   Jugador      Personaje    │
      │             │        │
 Entrada +      Ruta         │
 salto + cámara  + gravedad   │
      └──────┬──────┴────────┘
             │
        Locomoción
```

## Qué contiene [#qué-contiene]

<Cards>
  <Card title="Controladores" href="/docs/personaje/v1/fundamentos/controladores">
    Diferencia entre un personaje que sigue una ruta y un jugador controlado por entrada.
  </Card>

  <Card title="Locomoción y sensores" href="/docs/personaje/v1/fundamentos/locomocion-y-sensores">
    Movimiento, suelo, pendientes, techo y detección física.
  </Card>

  <Card title="Animación y audio" href="/docs/personaje/v1/fundamentos/animacion-y-audio">
    Cómo el estado de movimiento alimenta Animator y sonidos de pasos, salto y eventos.
  </Card>

  <Card title="Referencia" href="/docs/personaje/v1/referencia">
    API y clases principales del módulo.
  </Card>
</Cards>

## Primer recorrido [#primer-recorrido]

<div className="fd-steps">
  <div className="fd-step">
    ### Instalar el módulo [#instalar-el-módulo-step]

    Añade Personaje mediante el administrador de paquetes de Pandora Crystal o integra el contenido del repositorio en el proyecto correspondiente.
  </div>

  <div className="fd-step">
    ### Preparar un personaje [#preparar-un-personaje-step]

    Configura la base de `Personaje`, su `Locomocion`, sensores y modelo visual. Consulta [Configurar un personaje](/docs/personaje/v1/guias/configurar-personaje).
  </div>

  <div className="fd-step">
    ### Crear un jugador [#crear-un-jugador-step]

    Usa `Jugador` cuando el movimiento dependa de una entrada humana. Consulta [Configurar un jugador](/docs/personaje/v1/guias/configurar-jugador).
  </div>

  <div className="fd-step">
    ### Añadir una ruta [#añadir-una-ruta-step]

    Para personajes que recorren puntos predefinidos, utiliza `PuntoRuta` y la lista `camino` de `Personaje`.
  </div>
</div>

## Dependencias [#dependencias]

La versión actual del paquete declara dependencias sobre `com.pandora.modulo.motor` **1.1.4.0** y `com.pandora.modulo.nucleo` **1.3.6.1**. La versión del módulo Personaje registrada en el paquete es **1.4.8.2**.

## Qué leer después [#qué-leer-después]

[Requisitos](/docs/personaje/v1/requisitos) → [Fundamentos](/docs/personaje/v1/fundamentos) → [Guías](/docs/personaje/v1/guias) → [Referencia](/docs/personaje/v1/referencia)
