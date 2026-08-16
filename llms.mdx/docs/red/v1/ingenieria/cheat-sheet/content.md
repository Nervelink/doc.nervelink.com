# Referencia rápida (/docs/red/v1/ingenieria/cheat-sheet)



| Necesito...                 | Utiliza...                    |
| --------------------------- | ----------------------------- |
| Solicitar una acción        | RFC                           |
| Sincronizar estado mutable  | Estado sincronizado           |
| Dirigir una comunicación    | Objetivo                      |
| Separar ámbitos de juego    | Canal                         |
| Crear una entidad de red    | Objeto                        |
| Separar responsabilidades   | Componente                    |
| Identificar un participante | Jugador                       |
| Guardar estado              | Persistencia                  |
| Trabajar con bytes          | Buffer                        |
| Describir un mensaje        | Paquete                       |
| Serializar tipos            | Serialización                 |
| Suavizar movimiento         | Presentación / TransformSuave |
| Integrar Steam              | Steam                         |
| Diagnosticar                | Herramientas de diagnóstico   |

## Preguntas de diseño [#preguntas-de-diseño]

Antes de implementar una funcionalidad responde:

1. ¿Quién tiene autoridad?
2. ¿Es una intención o un estado?
3. ¿Quién necesita recibirlo?
4. ¿Debe persistir?
5. ¿Puede reconstruirse localmente?
6. ¿Qué ocurre si el propietario se desconecta?
7. ¿Cuánto costará con 100, 500 o 1000 entidades?

Si no puedes responderlas, todavía falta diseñar la parte de red.
