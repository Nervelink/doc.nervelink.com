# RMR (/docs/red/v1/comunicacion/rmr)



# RMR — Red Método Remoto [#rmr--red-método-remoto]

En Eco, el concepto que en TNet se conoce como &#x2A;*RFC (Remote Function Call)** ha sido refactorizado y renombrado como &#x2A;*RMR (Red Método Remoto)**.

```text
TNet                 Eco
─────────────────────────────
RFC  Remote Function Call  →  RMR  Red Método Remoto
```

La función es conceptualmente similar: invocar un método asociado a un objeto remoto. La nomenclatura que debe utilizar la documentación y el código de Eco es `RMR`.

<Callout title="Terminología de Eco" type="info">
  En Eco no utilices «RFC» para referirte a la API actual. «RFC» queda reservado para explicar equivalencias históricas con TNet.
</Callout>

## Flujo [#flujo]

```text
Componente
   ↓
Objeto
   ↓
RMR
   ↓
Paquete
   ↓
Servidor / transporte
   ↓
Destinatario
   ↓
Objeto remoto
   ↓
Método
```

## Cuándo utilizar RMR [#cuándo-utilizar-rmr]

RMR representa una operación o hecho que debe ejecutarse remotamente. Ejemplos:

* solicitar una acción;
* activar una habilidad;
* notificar un evento puntual;
* ordenar una operación de gameplay;
* coordinar una acción entre participantes.

Para valores que deben poder consultarse en cualquier momento utiliza estado sincronizado, no RMR como sustituto.

## Identificación [#identificación]

Eco conserva mecanismos heredados de TNet para identificar métodos por nombre o mediante identificadores compactos. El detalle de la representación pertenece al protocolo y no debería formar parte de la lógica de gameplay.

## Relación con las otras operaciones remotas [#relación-con-las-otras-operaciones-remotas]

| Operación Eco | Significado         | TNet  |
| ------------- | ------------------- | ----- |
| `RCR`         | Red Creación Remota | `RCC` |
| `RMR`         | Red Método Remoto   | `RFC` |
| `RCL`         | Red Creación Local  | `LCR` |

## Ejemplo conceptual [#ejemplo-conceptual]

```csharp
public class Torre : Componente
{
    public void SolicitarAtaque()
    {
        // La llamada remota representa una acción.
        ero.Send("Atacar", Objetivo.Otros);
    }
}
```

La validación de la acción continúa siendo responsabilidad del lado autoritativo correspondiente.

## RMR no significa «estado sincronizado» [#rmr-no-significa-estado-sincronizado]

No utilices RMR para enviar continuamente valores como:

```text
vida = 80
vida = 79
vida = 78
vida = 77
...
```

Ese patrón representa estado. La sincronización de estado debe modelarse como tal.

## Relación con TNet [#relación-con-tnet]

TNet conserva la terminología histórica `RFC`. Eco la ha refactorizado como `RMR` para expresar explícitamente que se trata de una red que ejecuta un método remoto.

<Cards>
  <Card title="RCR" href="/docs/red/v1/comunicacion/rcr">
    Creación remota de objetos.
  </Card>

  <Card title="RCL" href="/docs/red/v1/comunicacion/rcl">
    Solicitud de creación local en red.
  </Card>

  <Card title="Sincronización" href="/docs/red/v1/comunicacion/sincronizacion">
    Estado que debe mantenerse coherente.
  </Card>
</Cards>
