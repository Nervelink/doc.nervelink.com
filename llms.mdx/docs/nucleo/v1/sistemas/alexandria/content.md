# Alexandria (/docs/nucleo/v1/sistemas/alexandria)



# Alexandria [#alexandria]

Alexandria es el núcleo de inicialización del motor. Su función es descubrir, instanciar y coordinar todos los sistemas del juego de forma automática mediante reflexión.

## Responsabilidad principal [#responsabilidad-principal]

* Descubrir sistemas marcados con `SistemaAttribute`
* Instanciar sistemas como `MonoBehaviour`
* Ordenar la inicialización por prioridad
* Garantizar ciclo de vida controlado (Init / Update / Shutdown)

***

## Sistema de descubrimiento [#sistema-de-descubrimiento]

Alexandria utiliza reflexión sobre el ensamblado actual:

* Filtra clases concretas (no abstractas)
* Requiere `MonoBehaviour`
* Requiere implementación de `ISistema`
* Requiere `SistemaAttribute`

Esto permite registro automático sin configuración manual.

***

## Sistema de atributos [#sistema-de-atributos]

### SistemaAttribute [#sistemaattribute]

Define metadata del sistema.

```csharp
[Sistema(int prioridad, string nombre)]
```
