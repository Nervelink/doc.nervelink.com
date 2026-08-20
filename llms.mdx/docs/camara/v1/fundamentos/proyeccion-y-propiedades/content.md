# Proyección y propiedades (/docs/camara/v1/fundamentos/proyeccion-y-propiedades)



La vista activa determina si la cámara trabaja en proyección ortográfica o perspectiva. `Camara` aplica este dato sobre la `Camera` de Unity y actualiza `orthographicSize` o `fieldOfView` según corresponda. fileciteturn414file0

```text
Vista
 ├── Ortográfica → dimension → orthographicSize
 └── Perspectiva  → FOV       → fieldOfView
```

Durante una transición, las propiedades pueden interpolarse hasta alcanzar el valor de la vista destino. Esto evita que cambiar de perspectiva a ortográfica implique necesariamente un salto visual.
