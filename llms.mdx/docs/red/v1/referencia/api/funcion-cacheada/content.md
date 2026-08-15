# FuncionCacheada (/docs/red/v1/referencia/api/funcion-cacheada)



# FuncionCacheada [#funcioncacheada]

`FuncionCacheada` encapsula un método descubierto mediante reflexión y conserva su información de parámetros para evitar repetir trabajo al ejecutarlo.

Eco la utiliza en rutas donde una operación llega identificada dinámicamente, como llamadas remotas y otras funciones descubiertas por atributos.

## Modelo [#modelo]

```text
Nombre / ID de función
        ↓
Reflection
        ↓
FuncionCacheada
 ├── objeto objetivo
 ├── MethodInfo
 ├── parámetros
 └── tipos esperados
```

## Ejecutar [#ejecutar]

```csharp
object resultado = funcion.Execute(valor1, valor2);
```

La ejecución establece `success` según el resultado del `Invoke`.

## Conversión automática [#conversión-automática]

Si los tipos recibidos no coinciden exactamente con los parámetros declarados, la implementación puede intentar convertirlos mediante `Serializacion.CastValue`.

Esto permite cierta tolerancia entre versiones del protocolo o representaciones compatibles.

<Callout title="No dependas de la conversión implícita" type="warn">
  Los parámetros de una RFC deben diseñarse con tipos estables. La conversión automática es una capacidad de compatibilidad, no una sustitución de un contrato de red bien definido.
</Callout>

## `RMR` y `RCR` [#rmr-y-rcr]

`FuncionCacheada` participa en el mecanismo que descubre y ejecuta métodos marcados con atributos de red:

```csharp
[RMR]
void RecibirMensaje(string mensaje)
{
}
```

```csharp
[RCR]
static GameObject CrearUnidad(Vector3 posicion)
{
    return ...;
}
```

`RMR` representa una llamada remota; `RCR`, una rutina utilizada durante la creación remota de objetos.

## Identificación de RMR [#identificación-de-rmr]

Una `RMR` puede utilizar un ID numérico opcional o una propiedad para distinguir varios métodos con el mismo nombre dentro del mismo objeto.

Usar IDs puede ahorrar bytes en rutas extremadamente frecuentes, pero dificulta el diagnóstico y acopla más el protocolo al código.

## Referencias [#referencias]

<Card title="Comunicación · RFC" href="/docs/red/v1/comunicacion/rfc">
  Concepto de llamadas remotas desde gameplay.
</Card>

<Card title="Código fuente" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Comun/FuncionCacheada.cs">
  Implementación actual de `FuncionCacheada`, `RMR` y `RCR`.
</Card>
