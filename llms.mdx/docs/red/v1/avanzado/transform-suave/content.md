# TransformSuave (/docs/red/v1/avanzado/transform-suave)



# TransformSuave [#transformsuave]

`TransformSuave` está pensado para ocultar la naturaleza discreta de las actualizaciones de red. En lugar de aplicar cada muestra inmediatamente, utiliza el historial recibido para mover la representación visual de forma continua.

## Problema [#problema]

```text
Red
  muestra A──────muestra B──────muestra C
       ↓              ↓              ↓
gameObject ──salto───salto──────────salto
```

La interpolación convierte las muestras discretas en movimiento continuo.

## Patrón [#patrón]

```text
Estado recibido
      ↓
Guardar muestra
      ↓
Calcular tiempo objetivo
      ↓
Interpolar
      ↓
Transform visual
```

## Cuándo usarlo [#cuándo-usarlo]

Es apropiado para:

* personajes remotos;
* proyectiles visuales;
* objetos decorativos;
* cámaras o elementos que sólo deben seguir una posición remota.

Para un `Rigidbody` utiliza [Sincronizar Rigidbody](/docs/red/v1/avanzado/sincronizar-rigidbody).

## Autoridad [#autoridad]

`TransformSuave` no decide quién es propietario. Sólo suaviza la representación local de un estado que procede de la red.

<Callout title="Interpolación no es corrección de estado" type="info">
  El suavizado puede ocultar pequeños errores visuales, pero no corrige una discrepancia lógica. La autoridad y sincronización deben estar resueltas antes de interpolar.
</Callout>

## Fuente [#fuente]

<Card title="TransformSuave.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Cliente/TransformSuave.cs">
  Implementación actual.
</Card>
