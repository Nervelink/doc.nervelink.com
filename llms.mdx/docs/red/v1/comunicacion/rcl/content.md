# RCL (/docs/red/v1/comunicacion/rcl)



# RCL — Red Creación Local [#rcl--red-creación-local]

En Eco, el concepto histórico `LCR` (*Local Creation Request / Call*) de TNet se denomina **RCL — Red Creación Local**.

```text
TNet                           Eco
────────────────────────────────────────
LCR  Local Creation Request   →  RCL  Red Creación Local
     / Call
```

RCL pertenece al conjunto de mecanismos de creación y sirve para representar la solicitud de que una entidad sea creada localmente en el contexto adecuado de red.

## Diferencia con RCR [#diferencia-con-rcr]

```text
RCL
 ↓
Solicitud de creación local

RCR
 ↓
Creación remota de un objeto
```

La diferencia no debe entenderse únicamente como «cliente» frente a «servidor». Lo relevante es quién origina la operación y qué parte del flujo de creación debe resolverse de forma local o remota.

## Ciclo conceptual [#ciclo-conceptual]

```text
Componente / sistema
        ↓
       RCL
        ↓
Resolver contexto local
        ↓
Crear / registrar objeto
        ↓
Integrarlo en Eco
```

## Cuándo documentarlo [#cuándo-documentarlo]

RCL es especialmente útil cuando se describen workflows de creación donde el código local inicia la incorporación de una entidad al modelo de red.

Para una integración habitual, el desarrollador debería pensar primero en el resultado deseado —crear un objeto de red válido— y después elegir el mecanismo de creación adecuado.

<Cards>
  <Card title="RCR" href="/docs/red/v1/comunicacion/rcr">
    Red Creación Remota.
  </Card>

  <Card title="RMR" href="/docs/red/v1/comunicacion/rmr">
    Red Método Remoto.
  </Card>

  <Card title="Instanciar y destruir" href="/docs/red/v1/guias/instanciar-destruir">
    Workflow práctico del ciclo de vida de un objeto.
  </Card>
</Cards>
