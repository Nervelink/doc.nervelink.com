# Requisitos y limitaciones (/docs/red/v1/requisitos)



# Requisitos y limitaciones [#requisitos-y-limitaciones]

Esta página define las condiciones que debes comprobar antes de construir gameplay dependiente de Eco. La implementación del repositorio `Nervelink/eco` es la fuente de verdad cuando exista una diferencia con ejemplos antiguos de TNet.

<Callout title="No asumas compatibilidad por herencia" type="warn">
  Que TNet documente una API no significa que Eco la exponga con el mismo nombre, ciclo de vida o restricciones.
</Callout>

## Matriz de integración [#matriz-de-integración]

<Tabs items="['Cliente', 'Servidor', 'Modo local']">
  <Tab value="Cliente">
    Requiere la parte cliente de Eco, sus dependencias de Unity y acceso a la fachada `Eco`/`ClienteJuego` que utilice tu proyecto.
  </Tab>

  <Tab value="Servidor">
    Requiere `ServidorJuego`, configuración de persistencia y los transportes habilitados por la build objetivo.
  </Tab>

  <Tab value="Modo local">
    Puede combinar cliente y servidor dentro del mismo proceso para pruebas sin sockets.
  </Tab>
</Tabs>

## Componentes del entorno [#componentes-del-entorno]

```text
Unity
├── Código de Eco
├── Runtime cliente
├── Runtime servidor (si aplica)
└── Configuración de transporte
```

La ruta principal del código de red en el repositorio es:

```text
src/Assets/Pandora/Logica/Nucleo/Core/Red
```

## Transporte [#transporte]

<Tabs items="['TCP', 'UDP']">
  <Tab value="TCP">
    Es el transporte principal del cliente y proporciona el camino fiable de conexión y protocolo.
  </Tab>

  <Tab value="UDP">
    Es opcional y está pensado para determinados paquetes frecuentes o sensibles a latencia. Su uso depende de la plataforma y de la configuración compilada.
  </Tab>
</Tabs>

## Limitaciones del modelo [#limitaciones-del-modelo]

<Cards>
  <Card title="Canales">
    Son ámbitos lógicos, no conexiones físicas. Un cliente puede participar en varios simultáneamente.
  </Card>

  <Card title="Ownership">
    La visibilidad de un objeto no otorga autoridad para modificarlo.
  </Card>

  <Card title="Transiciones">
    Entrada a canales y cambios de nivel pueden dejar operaciones temporalmente pendientes.
  </Card>

  <Card title="Servidor local">
    El modo sin sockets no reproduce todas las condiciones de una conexión real entre procesos.
  </Card>
</Cards>

## Rendimiento [#rendimiento]

La red no debe modelarse como una extensión directa de `Update()`.

```text
Update de Unity
   │
   ├── ¿Cambió el dato?
   │       │
   │       └── no → no transmitir
   │
   └── sí
        │
        ├── ¿Necesita sincronización?
        ├── ¿Con qué frecuencia?
        ├── ¿Quién tiene autoridad?
        └── ¿Debe persistir?
```

Los sistemas automáticos que utilizan reflexión, como `AutoSincronizar`, son convenientes pero deben evaluarse con el perfil real de la aplicación.

## Seguridad y autoridad [#seguridad-y-autoridad]

Eco proporciona infraestructura de comunicación, no reglas de confianza de tu juego.

<Callout title="El servidor sigue siendo la autoridad crítica" type="error">
  No aceptes directamente desde el cliente valores que puedan decidir resultados importantes sin validación. Una RFC correctamente deserializada puede seguir siendo una petición inválida para tu juego.
</Callout>

## Escenas y canales [#escenas-y-canales]

El estado de red y el estado de Unity no son intercambiables. Una escena puede cambiar mientras un canal conserva identidad y estado lógico.

Por eso los flujos de [cambio de nivel](/docs/red/v1/guias/cambiar-nivel) y [transferencia de objetos](/docs/red/v1/guias/transferir-objeto-canal) deben tratarse como operaciones de red coordinadas.

## Qué debe considerarse estable [#qué-debe-considerarse-estable]

| Parte                    | Recomendación                                       |
| ------------------------ | --------------------------------------------------- |
| API de alto nivel de Eco | Utilízala como contrato de gameplay                 |
| Implementación interna   | Puede evolucionar sin aviso de API estable          |
| TNet upstream            | Referencia histórica/comparativa                    |
| Ejemplos antiguos        | No utilizarlos como fuente normativa                |
| DeepWiki                 | Útil para explorar, pero verificar contra el código |

## Checklist [#checklist]

<div className="fd-steps">
  <div className="fd-step">
    ### Compatibilidad [#compatibilidad-step]

    Comprueba la versión de Unity y la build objetivo del proyecto que integra Eco.
  </div>

  <div className="fd-step">
    ### Dependencias [#dependencias-step]

    Verifica que cliente, servidor y paquetes de transporte requeridos estén presentes.
  </div>

  <div className="fd-step">
    ### Autoridad [#autoridad-step]

    Define quién puede modificar cada estado antes de diseñar las RFC y sincronizaciones.
  </div>

  <div className="fd-step">
    ### Persistencia [#persistencia-step]

    Decide qué estado debe sobrevivir a la salida de todos los jugadores.
  </div>

  <div className="fd-step">
    ### Transporte [#transporte-step]

    Elige TCP o UDP según las garantías que realmente necesite la operación.
  </div>
</div>

## Fuente de verdad [#fuente-de-verdad]

Cuando exista una contradicción entre documentación, TNet antiguo y Eco:

```text
Código actual de Eco
        ↑
Implementación real
        ↑
Documentación de Eco
        ↑
TNet / DeepWiki como referencia
```

<Cards>
  <Card title="Eco" href="https://github.com/Nervelink/eco">
    Código fuente actual.
  </Card>

  <Card title="TNet upstream" href="https://github.com/tasharen/tnet">
    Evolución del proyecto original.
  </Card>

  <Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
    Exploración adicional del upstream.
  </Card>
</Cards>
