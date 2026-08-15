# UPnP y NAT (/docs/red/v1/avanzado/upnp)



# UPnP y NAT [#upnp-y-nat]

`UPnP` permite a una aplicación solicitar al router la creación de un mapeo de puertos sin que el usuario tenga que configurar manualmente el forwarding.

## El problema [#el-problema]

Un servidor puede escuchar correctamente en su máquina y, sin embargo, no ser accesible desde Internet porque el router utiliza NAT.

```text
Internet
   │
Router NAT
   │
   └── PC / servidor
```

El puerto debe estar accesible desde el exterior para que clientes remotos puedan conectarse directamente.

## Qué aporta UPnP [#qué-aporta-upnp]

```text
Servidor
   ↓
UPnP
   ↓
Router
   ↓
Regla NAT
   ↓
Puerto público → servidor local
```

No todos los routers admiten UPnP y algunas redes lo deshabilitan por seguridad.

## Flujo recomendado [#flujo-recomendado]

<Steps>
  <Step>
    ### Detectar soporte [#detectar-soporte]

    Comprueba que exista un dispositivo UPnP compatible antes de intentar crear reglas.
  </Step>

  <Step>
    ### Resolver dirección [#resolver-dirección]

    Determina la dirección LAN y el puerto TCP/UDP que utilizará Eco.
  </Step>

  <Step>
    ### Crear mapeo [#crear-mapeo]

    Solicita al router el forwarding para el puerto correspondiente.
  </Step>

  <Step>
    ### Verificar desde fuera [#verificar-desde-fuera]

    La existencia de la regla no garantiza que el ISP o firewall permita el tráfico. Prueba desde otra red.
  </Step>
</Steps>

<Callout title="UPnP no sustituye un diagnóstico de red" type="warn">
  CGNAT, firewalls, doble NAT o políticas del router pueden impedir conexiones entrantes aunque la solicitud UPnP sea correcta.
</Callout>

<Card title="UPnP.cs" href="https://github.com/Nervelink/eco/blob/main/src/Assets/Pandora/Logica/Nucleo/Core/Red/Utilidades/UPnP.cs">
  Implementación actual de las utilidades UPnP.
</Card>
