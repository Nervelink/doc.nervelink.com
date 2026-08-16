# Compatibilidad (/docs/red/v1/ingenieria/compatibilidad)



La compatibilidad no depende sólo de que un proyecto compile. Deben comprobarse versión de Unity, backend de scripting, plataformas, transporte y dependencias externas.

## Lista de comprobación [#lista-de-comprobación]

* Versión de Unity utilizada por el proyecto.
* API de .NET disponible.
* Plataforma objetivo.
* Arquitectura x86/x64/ARM cuando corresponda.
* Transporte seleccionado.
* Integración de Steam si se utiliza.
* Plugins de serialización o networking externos.
* Restricciones de builds dedicadas.
* Código condicionado por plataforma.

## Eco y TNet upstream [#eco-y-tnet-upstream]

Eco procede de la arquitectura de TNet, pero su documentación y código pueden evolucionar independientemente. Las equivalencias nominales no garantizan equivalencia exacta de comportamiento.

## Regla para actualizar [#regla-para-actualizar]

Después de actualizar Eco, prueba al menos:

```text
Servidor
Cliente
2 clientes
4 clientes
Desconexión
Reconexión
Persistencia
Steam, si aplica
```

<Callout title="Versiones" type="warn">
  No asumas que una versión histórica de TNet documenta todas las capacidades actuales de Eco. Comprueba siempre la implementación y la documentación de la versión que realmente utilizas.
</Callout>
