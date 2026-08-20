# Crear una vista (/docs/camara/v1/guias/crear-vista)



Una vista debe encapsular la decisión de cámara y no la lógica general del componente `Camara`.

```text
Contexto del juego
      ↓
VistaCamara
      ├── posición
      ├── dirección
      ├── proyección
      └── propiedades
            ↓
          Camara
```

Esto permite tener varias vistas para un mismo `Camara`, por ejemplo gameplay, combate, minimapa o una vista especial para una habilidad.

La implementación de `Camara` activa y desactiva la vista anterior cuando se cambia de una a otra. fileciteturn414file0
