# Sincronizar Rigidbody (/docs/red/v1/avanzado/sincronizar-rigidbody)



# Sincronizar Rigidbody [#sincronizar-rigidbody]

`SincronizarRB` está orientado a objetos cuyo movimiento depende de la física de Unity. El objetivo no es transmitir cada detalle del motor físico, sino mantener una representación suficientemente próxima entre propietario y observadores.

## Modelo [#modelo]

```text
Propietario
Rigidbody local
    │
    ├── posición
    ├── rotación
    ├── velocidad
    └── velocidad angular
           │
           ▼
       Eco / red
           │
     ┌─────┴─────┐
     ▼           ▼
 Observador A  Observador B
```

## Autoridad [#autoridad]

El propietario es quien debe producir el estado físico autoritativo. Los demás clientes reciben actualizaciones y las aplican a su copia.

Esto evita que dos motores de física distintos intenten corregir simultáneamente el mismo objeto.

## Suavizado [#suavizado]

Las actualizaciones de red llegan discretamente, mientras que Unity simula continuamente. Por ello un sincronizador físico normalmente combina:

* recepción de muestras;
* interpolación;
* corrección cuando la diferencia supera un umbral;
* actualización de velocidad para continuar la simulación local.

## Cuándo utilizarlo [#cuándo-utilizarlo]

Es adecuado para:

* vehículos;
* proyectiles físicos;
* objetos arrastrables;
* props dinámicos;
* cuerpos que necesitan continuidad visual.

No es la mejor solución para entidades que realmente deberían ser controladas por un estado lógico determinista.

<Callout title="No sincronices física sin diseñar autoridad" type="warn">
  Enviar posición, velocidad y rotación desde todos los clientes crea una lucha de autoridades. Decide primero quién controla el cuerpo y qué datos son realmente necesarios.
</Callout>

## Relación con TransformSuave [#relación-con-transformsuave]

Para objetos sin simulación física, `TransformSuave` cubre una necesidad similar con una estrategia más sencilla de interpolación visual.

<Card title="SincronizarRB.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/SincronizarRB.cs">
  Implementación actual.
</Card>

<Card title="TransformSuave" href="/docs/red/v1/avanzado/transform-suave">
  Consulta el suavizado de transformaciones no físicas.
</Card>
