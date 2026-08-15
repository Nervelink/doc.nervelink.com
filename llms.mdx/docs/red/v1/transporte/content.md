# Transporte (/docs/red/v1/transporte)



# Transporte [#transporte]

Transporte es la capa situada debajo de la comunicación de gameplay. Su responsabilidad es convertir una operación en datos binarios y llevarlos hasta el receptor adecuado.

<Callout title="No diseñes gameplay contra el transporte" type="info">
  La mayoría del código de juego debería trabajar con objetos, RFC, datos y objetivos. `Paquete`, `Buffer` y TCP/UDP se utilizan cuando necesitas comprender o extender la infraestructura.
</Callout>

<Cards>
  <Card title="Paquetes" href="/docs/red/v1/transporte/paquetes">
    Qué significa cada operación del protocolo.
  </Card>

  <Card title="Buffers" href="/docs/red/v1/transporte/buffers">
    Cómo Eco escribe y lee bytes y gestiona memoria temporal.
  </Card>

  <Card title="Serialización" href="/docs/red/v1/transporte/serializacion">
    Cómo se convierten valores y estructuras en datos transmitibles.
  </Card>

  <Card title="TCP y UDP" href="/docs/red/v1/transporte/tcp-udp">
    Qué transporte usar y qué garantías ofrece cada uno.
  </Card>
</Cards>

## Flujo completo [#flujo-completo]

```text
Gameplay
   ↓
RFC / Estado
   ↓
Paquete
   ↓
Serialización
   ↓
Buffer
   ↓
TCP / UDP / conexión personalizada
   ↓
Buffer
   ↓
Deserialización
   ↓
Paquete
   ↓
Gameplay / Runtime
```

## Qué debes conocer como usuario de Eco [#qué-debes-conocer-como-usuario-de-eco]

<Tabs items="['Normal', 'Infraestructura']">
  <Tab value="Normal">
    Concéntrate en RFC, `Set/Get`, objetivos, canales y objetos. Eco se encarga de transportar los datos.
  </Tab>

  <Tab value="Infraestructura">
    Estudia `Paquete`, `Buffer`, serialización y protocolos cuando estés creando extensiones, depurando problemas de red o trabajando en el propio runtime.
  </Tab>
</Tabs>

## Fiabilidad [#fiabilidad]

TCP proporciona una conexión orientada a flujo y es el transporte principal de Eco. UDP puede utilizarse para determinadas operaciones donde la latencia es prioritaria y la pérdida de un paquete puede ser aceptable.

<Callout title="UDP no es un TCP más rápido" type="warn">
  Cambiar a UDP no convierte automáticamente una operación en fiable. La fiabilidad, orden y tolerancia a pérdidas dependen del protocolo que construyas encima.
</Callout>

## Depuración [#depuración]

Cuando un mensaje no llega, no saltes directamente a revisar sockets.

<div className="fd-steps">
  <div className="fd-step">
    ### Operación \[step] [#1-operación-step]

    Comprueba que realmente se está ejecutando la RFC, cambio de estado o solicitud esperada.
  </div>

  <div className="fd-step">
    ### Protocolo \[step] [#2-protocolo-step]

    Comprueba qué `Paquete` se genera y si el receptor sabe procesarlo.
  </div>

  <div className="fd-step">
    ### Datos \[step] [#3-datos-step]

    Inspecciona el contenido serializado y el tamaño del `Buffer`.
  </div>

  <div className="fd-step">
    ### Transporte \[step] [#4-transporte-step]

    Sólo después comprueba TCP, UDP, endpoints, puertos y estado de la conexión.
  </div>
</div>

## Referencias [#referencias]

<Cards>
  <Card title="Paquetes y protocolo" href="/docs/red/v1/transporte/paquetes-protocolo">
    Lectura conceptual de la enumeración `Paquete` y sus flujos.
  </Card>

  <Card title="Buffer en profundidad" href="/docs/red/v1/transporte/buffer-en-profundidad">
    Pooling, escritura, lectura y depuración de buffers.
  </Card>

  <Card title="TCP y UDP en profundidad" href="/docs/red/v1/transporte/tcp-udp-en-profundidad">
    Detalles de implementación y decisiones de transporte.
  </Card>
</Cards>
