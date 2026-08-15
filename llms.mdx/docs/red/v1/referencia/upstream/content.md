# TNet upstream (/docs/red/v1/referencia/upstream)



# TNet upstream [#tnet-upstream]

Eco parte de TNet 3 y conserva gran parte de su modelo de red. Esta referencia permite entender qué conceptos proceden de upstream y dónde consultar su implementación original.

<Callout title="Fuente de verdad" type="info">
  Para desarrollar con Eco, la fuente normativa es `Nervelink/eco`. TNet se utiliza como referencia histórica y arquitectónica.
</Callout>

## Repositorio [#repositorio]

<Card title="TNet · GitHub" href="https://github.com/tasharen/tnet">
  Repositorio oficial de TNet mantenido por Tasharen.
</Card>

## Qué conserva Eco [#qué-conserva-eco]

La base de Eco mantiene conceptos como:

* canales y jugadores;
* objetos de red y componentes;
* RFC y mensajes del protocolo;
* serialización mediante nodos y buffers;
* transporte TCP/UDP;
* persistencia y estado del servidor.

Eco modifica principalmente la nomenclatura y la organización para integrarlas en la arquitectura de Nervelink y Pandora.

## Consultar TNet sin confundir APIs [#consultar-tnet-sin-confundir-apis]

La documentación y ejemplos históricos de TNet pueden utilizar nombres que ya no existen en Eco. Cuando sea necesario consultar upstream, primero hay que traducir el concepto mediante la tabla de equivalencias de Eco.
