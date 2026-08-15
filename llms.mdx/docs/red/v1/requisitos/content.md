# Requisitos y limitaciones (/docs/red/v1/requisitos)



# Requisitos y limitaciones [#requisitos-y-limitaciones]

Esta página reúne las condiciones que pueden afectar a la integración de Eco. Los detalles concretos pueden cambiar junto con el repositorio `eco`, por lo que esta información debe contrastarse con el código actual.

## Requisitos generales [#requisitos-generales]

### Proyecto Unity [#proyecto-unity]

Eco forma parte de un proyecto Unity y su código está organizado dentro de `src/Assets/Pandora/Logica/Nucleo/Core/Red`.

### Código de cliente y servidor [#código-de-cliente-y-servidor]

La arquitectura separa explícitamente cliente, servidor y código común. Una integración que utilice únicamente una de estas partes debe conservar las dependencias necesarias para esa configuración.

### Transporte [#transporte]

TCP constituye el transporte principal. UDP es opcional y depende de que la plataforma y la configuración permitan su utilización.

## Limitaciones importantes [#limitaciones-importantes]

### Canales y conexiones [#canales-y-conexiones]

Un canal no es una conexión de red. Un mismo cliente puede participar en varios canales mientras mantiene una misma conexión, por lo que la lógica del juego no debería modelar `Canal` como si fuera equivalente a un socket.

### Autoridad [#autoridad]

La visibilidad de un objeto no implica propiedad. La autoridad y el `owner` deben diseñarse explícitamente, especialmente cuando varios clientes modifican el mismo estado.

### Escenas y cambios de canal [#escenas-y-cambios-de-canal]

Durante la entrada a un canal y los cambios de nivel existen estados transitorios en los que Eco evita procesar determinadas operaciones para impedir que datos de una escena anterior se apliquen fuera de contexto.

### UDP [#udp]

UDP no ofrece las garantías de TCP. Debe reservarse para datos cuya pérdida o reordenación intermedia sea aceptable. El uso de UDP no convierte automáticamente una operación en fiable.

### Reflexión [#reflexión]

Componentes de conveniencia como `AutoSincronizar` utilizan reflexión y comprobaciones periódicas. Son apropiados para prototipos y casos sencillos, pero los sistemas críticos de producción pueden necesitar una implementación especializada.

### Servidor local [#servidor-local]

El modo local sin sockets es útil para pruebas y ejecución dentro del mismo proceso, pero no reproduce todas las condiciones de una conexión real entre máquinas.

## Dependencias y configuración [#dependencias-y-configuración]

Las dependencias concretas deben revisarse en el proyecto `Nervelink/eco`, especialmente en:

```text
Packages/
ProjectSettings/
src/Assets/Pandora/Logica/Nucleo/Core/Red/
```

No debe asumirse que una API documentada en TNet antiguo está disponible exactamente igual en Eco.

## Fuente de verdad [#fuente-de-verdad]

Cuando exista una discrepancia entre esta documentación, ejemplos antiguos de TNet y el código de Eco, debe priorizarse el comportamiento del repositorio `Nervelink/eco`.

<Card title="Código fuente de Eco" href="https://github.com/Nervelink/eco">
  Repositorio actual de la implementación.
</Card>

<Card title="TNet upstream" href="https://github.com/tasharen/tnet">
  Referencia para el comportamiento heredado y su evolución.
</Card>
