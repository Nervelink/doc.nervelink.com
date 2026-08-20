# PosicionCamara (/docs/camara/v1/referencia/posicion-camara)



`PosicionCamara` forma parte de las estructuras auxiliares del módulo. Se utiliza para transportar información de posición de forma separada de la lógica de render y del controlador principal. El proyecto mantiene estas estructuras bajo `Mono/Estructuras`, junto a `CajaDebugCamara`. fileciteturn415file0

Separar los datos de posición de la implementación permite reutilizar configuraciones y herramientas de editor sin acoplarlas al componente `Camara`.
