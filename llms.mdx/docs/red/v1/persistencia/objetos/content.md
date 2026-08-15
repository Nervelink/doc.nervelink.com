# Objetos persistentes (/docs/red/v1/persistencia/objetos)



# Objetos persistentes [#objetos-persistentes]

Los objetos dinámicos pueden configurarse para conservarse en el servidor. La persistencia afecta a su existencia y reconstrucción, no sólo a un campo individual.

## Crear un objeto persistente [#crear-un-objeto-persistente]

El protocolo distingue tipos de creación donde el objeto puede quedar guardado en el servidor. Entre las variantes descritas por Eco/TNet están los objetos guardados cuyo ownership se reasigna cuando el propietario abandona y los que se destruyen al abandonar el propietario. fileciteturn227file0

## Estado del objeto [#estado-del-objeto]

El `Objeto` mantiene un `Nodo` con datos sincronizados. Durante la creación puede establecerse el árbol completo; después conviene modificar propiedades individuales mediante `Set`. fileciteturn235file0

```text
Creación
└── Estado inicial completo

Runtime
└── Set("ruta", valor)
```

## Ownership [#ownership]

Un objeto persistente puede sobrevivir al propietario. Por eso ownership y existencia no deben estar acoplados:

```text
Objeto
├── Existe en el canal ✅
└── Propietario actual → Jugador B
```

`Objeto.ownerID` y `owner` representan el propietario actual y permiten cambiarlo para objetos dinámicos cuando el jugador destino pertenece al canal. Los objetos estáticos no pueden cambiar de propietario mediante esta API. fileciteturn234file0

## Destrucción [#destrucción]

`DestroySelf()` elimina el objeto de red; la persistencia no implica que el objeto sea indestructible. La decisión de destruirlo debe formar parte de las reglas de la partida.

## Restauración [#restauración]

Cuando un canal vuelve a tener participantes, el servidor necesita reproducir el estado persistente suficiente para reconstruir los objetos y sus datos. Por eso las operaciones guardadas y los datos persistentes deben ser deterministas y compatibles con la versión actual del juego.

## Regla de diseño [#regla-de-diseño]

Un objeto persistente debería poder responder a esta pregunta:

> “Si ningún jugador estuviera conectado durante un tiempo, ¿qué información necesitaríamos para reconstruirlo correctamente?”

Si la respuesta son cientos de eventos transitorios, probablemente el modelo de persistencia necesita simplificarse.
