# Diagnóstico (/docs/red/v1/ingenieria/diagnostico)



Cuando algo no se sincroniza, no empieces cambiando RFC al azar. Sigue una cadena de comprobaciones.

## Objeto que no aparece [#objeto-que-no-aparece]

```text
¿El objeto fue creado?
  ↓
¿Está en el canal esperado?
  ↓
¿Tiene identidad válida?
  ↓
¿El cliente participa en ese canal?
  ↓
¿El estado se marcó para sincronización?
  ↓
¿El prefab / representación local existe?
```

## RFC que no funciona [#rfc-que-no-funciona]

```text
¿La RFC está registrada?
  ↓
¿Se invoca realmente?
  ↓
¿El destinatario es correcto?
  ↓
¿El objeto existe en ese extremo?
  ↓
¿Los parámetros son serializables?
  ↓
¿El servidor la acepta?
```

## Ownership incorrecto [#ownership-incorrecto]

Comprueba por separado:

1. quién es owner de Eco;
2. quién es propietario de gameplay;
3. quién tiene autoridad para ejecutar la acción;
4. qué ocurre si cambia el owner.

## Canal incorrecto [#canal-incorrecto]

Si dos participantes ven comportamientos diferentes, comprueba primero su pertenencia a canales antes de modificar el código de sincronización.

## Paquetes [#paquetes]

Cuando el problema parece transporte, observa:

* conexión;
* protocolo utilizado;
* paquetes enviados;
* paquetes recibidos;
* errores de serialización;
* desconexiones;
* latencia;
* tamaño y frecuencia.

<Callout title="Diagnóstico reproducible" type="info">
  Registra siempre jugador, canal, objeto, ownership, tipo de paquete y estado de conexión. Un log que sólo diga "no sincroniza" no permite reconstruir el fallo.
</Callout>
