# Editor y herramientas (/docs/red/v1/avanzado/editor-y-herramientas)



# Editor y herramientas [#editor-y-herramientas]

Eco no es sólo el runtime que ejecuta la red. El ecosistema conserva varias herramientas pensadas para acelerar la configuración, depuración, serialización y análisis del tráfico.

<Callout title="Runtime y editor son cosas distintas" type="info">
  Una herramienta de editor puede ayudar a generar, inspeccionar o preparar datos, pero no sustituye al runtime de `Eco`, `ClienteJuego` o `ServidorJuego`.
</Callout>

## Qué entra en esta categoría [#qué-entra-en-esta-categoría]

El material heredado de TNet agrupa aquí varias capacidades:

| Área          | Uso                                                                   |
| ------------- | --------------------------------------------------------------------- |
| Configuración | Preparar proyectos y requisitos de ejecución.                         |
| Diagnóstico   | Inspeccionar estado, paquetes, ancho de banda y errores.              |
| Serialización | Convertir datos complejos a formatos persistibles.                    |
| Runtime Code  | Compilar código en tiempo de ejecución cuando el proyecto lo permite. |
| Profiling     | Detectar operaciones o paquetes demasiado frecuentes.                 |

## Diagnóstico desde Eco [#diagnóstico-desde-eco]

La propia fachada expone información útil para herramientas internas:

```csharp
int enviados = Eco.PaquetesEnviados;
int recibidos = Eco.PaquetesRecibidos;
int bytesEnviados = Eco.BytesEnviados;
int bytesRecibidos = Eco.BytesRecibidos;
int ping = Eco.Ping;
```

Esto permite construir paneles de diagnóstico sin acceder directamente al protocolo.

## Perfilado de paquetes [#perfilado-de-paquetes]

El upstream incluye mecanismos de perfilado y contadores de paquetes. Eco mantiene parte de este enfoque y además expone contadores de tráfico a través de `Eco`.

Un patrón útil durante desarrollo es medir:

```text
frecuencia de RFC
      ↓
bytes enviados / recibidos
      ↓
frecuencia de sincronización
      ↓
coste de serialización
      ↓
impacto en CPU
```

<Callout title="No confundas más paquetes con más rendimiento" type="warn">
  Reducir paquetes puede disminuir tráfico, pero también puede aumentar el tamaño de cada mensaje o retrasar información. La optimización debe medirse con latencia, ancho de banda y CPU.
</Callout>

## Serialización y herramientas de datos [#serialización-y-herramientas-de-datos]

El sistema `Nodo` puede utilizarse para datos jerárquicos y persistencia. El upstream también documenta formatos de texto, binario y compresión LZMA. En Eco, estas capacidades deben considerarse parte de la capa de datos, no del transporte TCP/UDP.

```text
Nodo
 ↓
Serialización
 ├── texto
 ├── binario
 └── binario comprimido
```

## Runtime Code [#runtime-code]

El soporte de `RuntimeCode` permite compilar código durante la ejecución cuando esta funcionalidad está incluida y configurada en el proyecto.

No debe utilizarse como mecanismo normal para lógica de gameplay. Su utilidad está más relacionada con herramientas, extensibilidad y escenarios donde el código no puede distribuirse previamente.

<Card title="Runtime Code" href="/docs/red/v1/avanzado/runtime-code">
  Guía de integración y límites del código ejecutado dinámicamente.
</Card>

## Relación con TNet [#relación-con-tnet]

DeepWiki identifica herramientas de editor para inspeccionar `TNObject`, auto-sincronización, instancias de servidor y exportación de `DataNode`. En Eco, cada una debe considerarse según la implementación que realmente exista en el repositorio; la documentación no asume que una ventana histórica de TNet siga existiendo con el mismo nombre.

<Card title="DeepWiki · TNet" href="https://deepwiki.com/tasharen/tnet">
  Usar como referencia histórica de las herramientas upstream.
</Card>
