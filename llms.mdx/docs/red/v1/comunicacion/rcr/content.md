# RCR (/docs/red/v1/comunicacion/rcr)



# RCR — Red Creación Remota [#rcr--red-creación-remota]

En Eco, el concepto histórico `RCC` (*Remote Creation Call*) de TNet se denomina **RCR — Red Creación Remota**.

```text
TNet                       Eco
────────────────────────────────────
RCC  Remote Creation Call  →  RCR  Red Creación Remota
```

RCR se utiliza para resolver la creación de objetos que deben existir y ser conocidos por los participantes de una sesión.

<Callout title="No confundir con RCL" type="info">
  `RCR` describe la creación remota de un objeto. `RCL` describe una solicitud de creación local dentro del modelo de red de Eco.
</Callout>

## Ciclo conceptual [#ciclo-conceptual]

```text
Solicitud / condición de creación
        ↓
      RCR
        ↓
Servidor / autoridad
        ↓
Creación del Objeto
        ↓
Registro
        ↓
Distribución a participantes
```

## Cuándo aparece [#cuándo-aparece]

RCR es relevante para:

* aparición de unidades;
* creación de torres;
* generación de objetos persistentes;
* creación de objetos durante la restauración de una partida;
* sistemas que reciben una orden de creación desde la red.

La creación no debe reducirse a instanciar un `GameObject`: Eco también debe establecer la identidad, el canal, el registro y el estado necesario del objeto.

## Relación con Eco [#relación-con-eco]

RCR pertenece a la capa de creación de objetos de red. El gameplay debería expresar qué entidad necesita existir y dejar que la infraestructura de Eco resuelva el registro y la distribución.

<Cards>
  <Card title="RMR" href="/docs/red/v1/comunicacion/rmr">
    Ejecutar un método remoto sobre una entidad existente.
  </Card>

  <Card title="RCL" href="/docs/red/v1/comunicacion/rcl">
    Solicitud de creación local.
  </Card>

  <Card title="Objetos" href="/docs/red/v1/modelo/objetos">
    Identidad, ownership y ciclo de vida de los objetos de Eco.
  </Card>
</Cards>
