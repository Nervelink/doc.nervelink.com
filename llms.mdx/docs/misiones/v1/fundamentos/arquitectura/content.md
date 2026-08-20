# Arquitectura (/docs/misiones/v1/fundamentos/arquitectura)



`Misiones` está diseñado como una capa de datos apoyada sobre `Pandora.Motor`. El manifiesto declara una futura lógica de misión principal, secundarias y ramificaciones, pero la implementación actual visible en el repositorio se reduce a `DatosMisiones`. fileciteturn451file0

```text
Pandora.Motor
      │
      ▼
    IDatos
      │
      ▼
DatosMisiones
      │
      ▼
Resources/Datos/DatosMisiones
```

La separación es útil porque permite que la definición de misiones permanezca fuera de los sistemas de gameplay que posteriormente consuman esos datos.

<Callout title="Límite actual" type="warn">
  No existe actualmente en el árbol analizado un controlador de estado de misión, evaluador de objetivos, gestor de ramas ni resolvedor de recompensas. La documentación no los trata como API disponible.
</Callout>
