# Ejemplos (/docs/red/v1/ejemplos)



# Ejemplos [#ejemplos]

El repositorio de Eco conserva una colección de ejemplos heredados de TNet, pero el código actual ya utiliza la nomenclatura de Eco. Esta sección convierte esos ejemplos en documentación práctica y enlaza directamente con sus fuentes.

<Callout title="Ejemplos heredados, implementación actual" type="info">
  Los nombres de algunos PDFs y carpetas mantienen terminología de TNet. No significa que el runtime actual siga usando esa API. Para el comportamiento, consulta siempre el código C# de `eco`.
</Callout>

## Mapa de ejemplos [#mapa-de-ejemplos]

<Card title="AutoUnirse" href="/docs/red/v1/ejemplos/auto-unirse">
  Conexión y entrada automática a un canal al iniciar el juego.
</Card>

<Card title="AutoSincronizar" href="/docs/red/v1/ejemplos/auto-sincronizar">
  Sincronización automática de campos y propiedades de Unity.
</Card>

<Card title="AutoCrear" href="/docs/red/v1/ejemplos/auto-crear">
  Instanciación de un prefab al entrar en el canal apropiado.
</Card>

<Card title="RFC" href="/docs/red/v1/ejemplos/rfc">
  Patrón clásico de Remote Function Call.
</Card>

<Card title="Varios canales" href="/docs/red/v1/ejemplos/varios-canales">
  El ejemplo que demuestra la capacidad multicanal de Eco.
</Card>

## Ejemplos que todavía están en el repositorio [#ejemplos-que-todavía-están-en-el-repositorio]

Además de los recorridos anteriores, Eco conserva ejemplos de:

```text
Object Creation
Frequent Packets
Chat
Movement
Stress Test
Player Data
Custom Channel Data
DataNode
Runtime C# Code
Runtime C# Behaviours
Object Instantiation
```

Estos ejemplos no deben desaparecer sólo porque procedan de TNet. Son una fuente útil para descubrir comportamientos que todavía necesitan documentación específica.

## Siguiente paso [#siguiente-paso]

Cuando un ejemplo te explique cómo funciona una característica, usa después la página conceptual correspondiente de `Fundamentos`, `Comunicación`, `Transporte` o `Runtime`.
