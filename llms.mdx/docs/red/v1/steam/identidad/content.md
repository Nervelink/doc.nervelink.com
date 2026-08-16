# Identidad (/docs/red/v1/steam/identidad)



La integración expone una identidad Steam independiente de la identidad de red de Eco.

## Steam ID [#steam-id]

`Steam.IDUsuario` representa el identificador persistente del usuario dentro de Steam cuando Steam está activo.

## Nombre [#nombre]

`Steam.NombreUsuario` obtiene el nombre de Steam del usuario. El nombre no debe utilizarse como identificador porque puede cambiar y no tiene las propiedades de unicidad del Steam ID.

```text
Steam ID   → identidad
Nombre     → presentación
Jugador    → identidad dentro de Eco
Objeto     → entidad de red
```

El código actual distingue explícitamente `IDUsuario`, `NombreUsuario` y el `Jugador` de Eco. fileciteturn190file0

## Uso recomendado [#uso-recomendado]

Utiliza Steam ID para identificar al usuario externamente. Utiliza `Jugador` para la sesión Eco y los IDs de objetos para las entidades de gameplay.

No almacenes el nombre de Steam como clave primaria de una partida.
