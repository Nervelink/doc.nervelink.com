# Serialización avanzada (/docs/red/v1/transporte/serializacion-avanzada)



# Serialización avanzada [#serialización-avanzada]

Eco separa el modelo estructurado de datos del formato binario que viaja por la red. `Nodo` representa datos jerárquicos y `Buffer` representa los bytes; el protocolo decide qué debe escribirse y en qué orden.

## Compatibilidad [#compatibilidad]

Una modificación de un mensaje de red debe considerar tanto escritor como lector. Cambiar tipos, orden o estructura sin una estrategia de compatibilidad puede hacer que los receptores interpreten bytes de forma incorrecta.

```text
Writer
  int
  string
  object

        ↓ bytes ↓

Reader
  int
  string
  object
```

## IBinarySerializable [#ibinaryserializable]

Los tipos complejos pueden proporcionar su propia serialización binaria mediante las interfaces soportadas por Eco. Esto permite controlar el formato en lugar de depender de una conversión genérica.

## Nodo [#nodo]

`Nodo` es apropiado cuando el estado necesita estructura, nombres y jerarquía. Es especialmente útil para datos de servidor, canal, jugador y objeto.

## Rendimiento [#rendimiento]

Para tráfico frecuente, evita serializar árboles completos cuando sólo cambió una propiedad. Para datos de configuración o creación puede tener sentido enviar estructuras completas.

La implementación upstream también contempla compresión LZMA para determinados flujos de escritura y mejoras históricas para reducir asignaciones y costes de serialización. fileciteturn226file0

## Regla de versión [#regla-de-versión]

La documentación de Eco debe describir el formato actual del repositorio, no asumir que el formato histórico de TNet sigue siendo idéntico. Si un cambio de código altera el protocolo, la documentación de Transporte y Referencia debe revisarse al mismo tiempo.
