# Código en tiempo de ejecución (/docs/red/v1/ejemplos/runtime-code)



# Código en tiempo de ejecución [#código-en-tiempo-de-ejecución]

`RuntimeCode` permite compilar y cargar código durante la ejecución. Es una capacidad avanzada heredada del ecosistema de TNet y debe utilizarse con un objetivo concreto.

<Callout title="No es la vía normal de desarrollo" type="warn">
  El código de gameplay principal debe compilarse con el proyecto. `RuntimeCode` resulta adecuado para herramientas, scripting controlado, extensiones o escenarios donde el contenido no puede conocerse por adelantado.
</Callout>

## Flujo conceptual [#flujo-conceptual]

```text
Código fuente
   ↓
RuntimeCode
   ↓
Compilación
   ↓
Assembly
   ↓
Registro de tipos
   ↓
Uso durante la sesión
```

## Cuándo utilizarlo [#cuándo-utilizarlo]

* herramientas internas
* prototipos de lógica distribuible
* sistemas de modding controlado
* generación dinámica de comportamiento

## Cuándo evitarlo [#cuándo-evitarlo]

No lo utilices para sustituir el sistema de escenas, prefabs o componentes normales si el código ya puede distribuirse con el juego.

## Seguridad [#seguridad]

El código compilado durante la ejecución tiene las mismas implicaciones de seguridad que cualquier sistema de scripting dinámico. No ejecutes código recibido de clientes no confiables.

<Card title="Runtime Code avanzado" href="/docs/red/v1/avanzado/runtime-code">
  Configuración y detalles internos.
</Card>

<Card title="Ejemplos" href="/docs/red/v1/ejemplos">
  Más patrones avanzados de Eco.
</Card>
