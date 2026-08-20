# Arquitectura (/docs/camara/v1/fundamentos/arquitectura)



`Camara` actúa como orquestador. Mantiene una `VistaCamara` activa, calcula posición y dirección, aplica transiciones, suavizado y cambios de proyección, y coordina el render auxiliar utilizado por el difuminado. fileciteturn412file0

```text
VistaCamara
    ↓
Camara
├── posición
├── rotación
├── proyección
├── transición
├── suavizado
└── difuminado
```

La arquitectura separa las decisiones de la vista de la ejecución física de la cámara. Esto permite reutilizar una misma implementación para cámara normal, minimapa, combate u otros estilos declarados por el módulo. fileciteturn411file0
