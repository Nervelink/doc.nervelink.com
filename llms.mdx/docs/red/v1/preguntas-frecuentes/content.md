# Preguntas frecuentes (/docs/red/v1/preguntas-frecuentes)



# Preguntas frecuentes [#preguntas-frecuentes]

## ¿Eco es una implementación nueva de networking? [#eco-es-una-implementación-nueva-de-networking]

No. Eco parte de TNet 3 y conserva gran parte de su arquitectura y comportamiento, pero modifica la nomenclatura, la organización y determinadas abstracciones para adaptarlas a Nervelink.

## ¿Puedo utilizar documentación antigua de TNet? [#puedo-utilizar-documentación-antigua-de-tnet]

Sí, como referencia conceptual. No debe tratarse como documentación normativa de Eco. Las APIs, nombres y comportamientos deben comprobarse contra el repositorio actual de `eco`.

## ¿Un cliente sólo puede estar en un canal? [#un-cliente-sólo-puede-estar-en-un-canal]

No. Eco mantiene una colección de canales por cliente y permite participar simultáneamente en varios canales.

## ¿Canal y conexión son lo mismo? [#canal-y-conexión-son-lo-mismo]

No. La conexión representa el vínculo de transporte con el servidor. El canal representa un ámbito de estado compartido dentro de esa conexión.

## ¿Qué diferencia hay entre `Objeto` y `Componente`? [#qué-diferencia-hay-entre-objeto-y-componente]

`Objeto` proporciona la identidad y el estado de red. `Componente` implementa comportamiento asociado a ese objeto.

```text
Objeto
└── Componente
```

## ¿Qué debería utilizar para enviar una acción? [#qué-debería-utilizar-para-enviar-una-acción]

Normalmente una RFC o mecanismo equivalente de comunicación de alto nivel. Una acción puntual no debería modelarse como una sincronización continua de estado.

## ¿Y para mantener una propiedad sincronizada? [#y-para-mantener-una-propiedad-sincronizada]

Utiliza el sistema de datos y sincronización apropiado. Por ejemplo, `Set` puede representar el cambio de una propiedad de un `Objeto`, mientras que `AutoSincronizar` puede servir para prototipos o casos sencillos.

## ¿TCP y UDP se pueden utilizar indistintamente? [#tcp-y-udp-se-pueden-utilizar-indistintamente]

No. TCP proporciona transporte fiable y ordenado; UDP está pensado para casos donde se acepta pérdida o reordenación de paquetes. La elección depende de la semántica del dato.

## ¿Puedo ejecutar cliente y servidor dentro de la misma instancia de Unity? [#puedo-ejecutar-cliente-y-servidor-dentro-de-la-misma-instancia-de-unity]

Sí. Eco dispone de un modo de servidor local sin sockets. Es útil para pruebas y escenarios locales, aunque no debe considerarse equivalente a una conexión real entre máquinas.

## ¿Qué debo consultar cuando TNet y Eco parecen comportarse de forma diferente? [#qué-debo-consultar-cuando-tnet-y-eco-parecen-comportarse-de-forma-diferente]

El código actual de `Nervelink/eco` tiene prioridad. TNet y DeepWiki deben utilizarse como referencia para entender el origen del diseño y localizar conceptos relacionados.

## ¿Dónde está el código principal de Eco? [#dónde-está-el-código-principal-de-eco]

La parte central de la red se encuentra principalmente en:

```text
src/Assets/Pandora/Logica/Nucleo/Core/Red/
```

Dentro se separan las áreas de cliente, común, servidor, serialización, transporte y elementos auxiliares.

## ¿Dónde puedo consultar la implementación upstream? [#dónde-puedo-consultar-la-implementación-upstream]

La implementación de referencia original está en TNet:

<Card title="TNet en GitHub" href="https://github.com/tasharen/tnet">
  Repositorio upstream.
</Card>

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Vista generada de la arquitectura del repositorio actual de TNet.
</Card>
