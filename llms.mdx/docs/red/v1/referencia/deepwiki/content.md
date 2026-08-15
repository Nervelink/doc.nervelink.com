# DeepWiki · TNet (/docs/red/v1/referencia/deepwiki)



# DeepWiki · TNet [#deepwiki--tnet]

DeepWiki puede ser útil para obtener una visión rápida y navegable del repositorio de TNet, especialmente cuando se necesita localizar una clase o entender las relaciones entre subsistemas.

<Card title="Abrir DeepWiki" href="https://deepwiki.com/tasharen/tnet">
  Explorar la documentación generada del repositorio actual de TNet.
</Card>

## Para qué utilizarlo [#para-qué-utilizarlo]

DeepWiki resulta especialmente útil para:

* localizar rápidamente una parte concreta del repositorio;
* obtener una visión general de una arquitectura extensa;
* descubrir relaciones entre clases y subsistemas;
* contrastar una implementación de Eco con su origen upstream.

## Qué no debe sustituir [#qué-no-debe-sustituir]

DeepWiki no sustituye al código de Eco ni a la documentación de esta versión. La implementación generada puede quedar desactualizada y puede describir nombres o flujos pertenecientes a TNet y no a Eco.

```text
DeepWiki
   ↓
orientación / investigación
   ↓
TNet upstream
   ↓
comparación
   ↓
Eco
   ↓
fuente normativa
```

## Flujo recomendado [#flujo-recomendado]

Cuando una página de Eco haga referencia a un concepto heredado, puede utilizarse DeepWiki para orientarse y después comprobar la implementación correspondiente en el repositorio de TNet y en `Nervelink/eco`.
