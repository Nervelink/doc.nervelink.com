# Inicio Rápido (/docs/red/v1)



<Callout title="¿Qué es Eco?" type="info">
  Eco es la capa de red de Pandora basada en TNet 3. Mantiene su modelo de conexión, canales, objetos, comunicación y sincronización, pero utiliza la nomenclatura y organización propias de Nervelink.
</Callout>

## Antes de empezar [#antes-de-empezar]

La documentación está escrita para el comportamiento actual de `Nervelink/eco`. Los ejemplos y conceptos de TNet se utilizan como referencia histórica y para facilitar la transición, pero la implementación de Eco es la fuente de verdad.

## Compatibilidad [#compatibilidad]

### Unity [#unity]

Eco está integrado como código de proyecto Unity. La compatibilidad concreta depende de la versión de Unity utilizada por el proyecto `eco` y de sus paquetes.

### Plataformas [#plataformas]

La disponibilidad de TCP, UDP y determinadas APIs puede variar según la plataforma y las directivas de compilación utilizadas por Eco.

### Proyecto [#proyecto]

La integración presupone la estructura y dependencias del proyecto que contiene Eco. Las páginas de integración explican qué partes son necesarias y cuáles son opcionales.

## Tu primer recorrido [#tu-primer-recorrido]

Para entender Eco sin entrar todavía en todos sus detalles internos, sigue este orden:

```text
Inicio rápido
     ↓
Fundamentos
     ↓
Modelo de red
     ↓
Comunicación
     ↓
Transporte
     ↓
Runtime
```

### 1. Entender la arquitectura [#1-entender-la-arquitectura]

Empieza por [Arquitectura](/docs/red/v1/fundamentos/arquitectura) para conocer la relación entre `Eco`, conexiones, canales, objetos, componentes y transporte.

### 2. Aprender el modelo de red [#2-aprender-el-modelo-de-red]

Continúa con [Conceptos](/docs/red/v1/fundamentos/conceptos) y después con [Modelo de red](/docs/red/v1/modelo), especialmente [Canales](/docs/red/v1/modelo/canales) y [Objetos](/docs/red/v1/modelo/objetos).

### 3. Elegir cómo comunicarte [#3-elegir-cómo-comunicarte]

[Comunicación](/docs/red/v1/comunicacion) explica la diferencia entre RFC, estado y sincronización.

### 4. Entender qué viaja por la red [#4-entender-qué-viaja-por-la-red]

[Transporte](/docs/red/v1/transporte) cubre `Paquete`, `Buffer`, serialización y los transportes TCP/UDP.

### 5. Ejecutar cliente y servidor [#5-ejecutar-cliente-y-servidor]

[Runtime](/docs/red/v1/runtime) explica cómo se comportan `ClienteJuego`, `ServidorJuego` y el modo local.

## Estructura mental de Eco [#estructura-mental-de-eco]

```text
                        Eco
                         │
       ┌─────────────────┼─────────────────┐
       │                 │                 │
   Fundamentos      Modelo de red     Comunicación
                         │                 │
                 ┌───────┴───────┐   ┌─────┴─────┐
                 │               │   │           │
              Canales         Objetos       RFC / Sync
                 │               │
                 └───────┬───────┘
                         │
                    Transporte
                         │
                    TCP / UDP
                         │
                    Runtime
              ┌──────────┴──────────┐
              │                     │
           Cliente               Servidor
```

## Siguiente paso [#siguiente-paso]

Antes de desarrollar una funcionalidad propia, consulta [Requisitos y limitaciones](/docs/red/v1/requisitos) para conocer las condiciones que pueden afectar a la integración.

<Card title="Requisitos y limitaciones" href="/docs/red/v1/requisitos">
  Compatibilidad, dependencias, restricciones conocidas y límites de la implementación.
</Card>

<Card title="Preguntas frecuentes" href="/docs/red/v1/preguntas-frecuentes">
  Respuestas rápidas a las dudas más habituales sobre Eco y su relación con TNet.
</Card>
