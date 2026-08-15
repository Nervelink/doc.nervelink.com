# Serialización en profundidad (/docs/red/v1/transporte/serializacion-profunda)



# Serialización en profundidad [#serialización-en-profundidad]

La serialización es el puente entre un objeto C# y los bytes que realmente viajan por la red. DeepWiki destaca `DataNode`, `Buffer` y las extensiones de serialización como una de las piezas centrales de TNet; en Eco la misma arquitectura se expresa mediante `Nodo`, `Buffer` y sus contratos de serialización.

## Dos representaciones principales [#dos-representaciones-principales]

### Buffer [#buffer]

`Buffer` es la representación binaria de transporte. Está orientado a escribir y leer valores en el orden exacto que espera el protocolo.

```text
C#
 │
 ├── writer.Write(...)
 └── writer.WriteArray(...)
        ↓
      Buffer
        ↓
      bytes
```

Al recibir datos ocurre el proceso inverso:

```text
bytes
 ↓
Buffer / BinaryReader
 ↓
r.Read(...)
r.ReadArray(...)
 ↓
C#
```

`Buffer` es apropiado para paquetes y mensajes que necesitan una representación compacta y determinista.

### Nodo [#nodo]

`Nodo` representa datos jerárquicos:

```text
Jugador
├── Nombre
├── Nivel
├── Inventario
│   ├── Espada
│   └── Pociones
└── Configuracion
    └── Volumen
```

Esto resulta especialmente útil para datos de jugador, servidor, canal y estado persistente.

## Serialización de tipos propios [#serialización-de-tipos-propios]

Eco admite contratos que permiten a un tipo C# convertir su estado en datos serializables y reconstruirse después. En el modo offline, `Eco.EstablecerDatosJugador` incluso fuerza el ciclo de serialización/deserialización para mantener un comportamiento equivalente al remoto en ciertos tipos.

La regla práctica es:

```text
¿Es un dato simple?
 ├── Sí → primitivas / arrays / tipos soportados
 │
 └── No
      ├── ¿Necesita estructura editable? → Nodo
      └── ¿Necesita formato binario propio? → contrato de serialización
```

## Orden y compatibilidad [#orden-y-compatibilidad]

Un paquete binario no tiene nombres de propiedades que permitan adivinar cómo leerlo. El emisor y el receptor deben coincidir en:

* tipo
* orden
* cantidad de valores
* representación utilizada

Por eso un cambio aparentemente inocente en el orden de escritura puede romper la lectura remota.

## Nodo no sustituye a Buffer [#nodo-no-sustituye-a-buffer]

`Nodo` y `Buffer` trabajan a niveles diferentes.

| Herramienta    | Problema que resuelve                       |
| -------------- | ------------------------------------------- |
| `Buffer`       | transporte binario y protocolo              |
| `Nodo`         | datos jerárquicos y estado estructurado     |
| `Paquete`      | identifica qué operación contiene el buffer |
| Serializadores | convierten tipos propios                    |

Evita convertir todo a `Nodo` sólo porque sea cómodo. Para tráfico frecuente, un formato binario específico suele ser mucho más apropiado.

## Compresión y formatos de persistencia [#compresión-y-formatos-de-persistencia]

La arquitectura heredada de TNet contempla diferentes formatos para datos persistentes, incluidos binario y LZMA. Eco conserva piezas de esa infraestructura, pero la elección de formato debe considerarse parte de persistencia y no de cada RFC individual.

La compresión suele ser interesante cuando el coste de almacenamiento o transferencia domina; para mensajes pequeños y frecuentes añade complejidad innecesaria.

## Serialización y seguridad [#serialización-y-seguridad]

La serialización no valida reglas de juego. Que un cliente pueda serializar `oro = 999999` no significa que el servidor deba aceptarlo.

```text
Serialización
    ≠
Validación
    ≠
Autorización
```

El servidor debe validar tamaños, tipos esperados, límites de gameplay y permisos antes de aplicar datos sensibles.

## Diagnóstico de errores [#diagnóstico-de-errores]

Cuando un paquete falla al reconstruirse, el error puede aparecer lejos del lugar donde se escribió originalmente. Para investigar:

<Steps>
  <Step>
    ### Captura el tipo de Paquete [#captura-el-tipo-de-paquete]

    Determina qué operación estaba siendo procesada.
  </Step>

  <Step>
    ### Comprueba el orden de lectura y escritura [#comprueba-el-orden-de-lectura-y-escritura]

    Compara exactamente `Write(...)` con `Read(...)`.
  </Step>

  <Step>
    ### Comprueba arrays y longitudes [#comprueba-arrays-y-longitudes]

    Un array leído con una estructura distinta desincroniza el resto del buffer.
  </Step>

  <Step>
    ### Comprueba el tipo [#comprueba-el-tipo]

    Asegúrate de que ambos lados usan la misma representación y serializer.
  </Step>
</Steps>

<Callout title="La compatibilidad binaria es una responsabilidad de la API" type="warn">
  No cambies el formato de un paquete crítico sin considerar clientes antiguos conectados, datos persistentes y cualquier punto donde Eco reenvíe o almacene el mensaje.
</Callout>

<Card title="Buffer" href="/docs/red/v1/transporte/buffers">
  API y conceptos básicos de Buffer.
</Card>

<Card title="Serialización avanzada" href="/docs/red/v1/transporte/serializacion-avanzada">
  Tipos propios y escenarios avanzados.
</Card>
