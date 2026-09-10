Defecto 1: Falta la instalación desde el bloqueo y los versionados, esto afecta la reproducibilidad y sin historial no se distingue el codigo nuevo del anterior.
Defecto 2: Falta la cobertura y el porcentaje de aceptación, se debe evaluar el porcentaje dispuesto a aceptar para recibir código de calidad que se va a desplegar a futuro.
Defecto 3: Falta de dependencia entre los jobs, no puedo publicar si no esta validado antes. SI para validar se necesita el artefacto publico, entonces primero se debe publicar el artefacto para poder validar. 
Hay una dependencia que falta "needs: X"
Defecto 4: Falta el caché de dependencias, para asi reducir los tiempos de ejecución.


El defecto que explica la duración:
En si los tiempos están en promedio con las mediciones realizadas en el trabajo previo, pero en si el caché de dependencias es el que reduce los tiempos de ejecución y afecta al lead time.

Dato del value stream map: Completo y Correcto y el defecto que lo ataca es la cobertura y el porcentaje, ya que en el Caso de Seguros Pacífico Sur se rechazan y devuelven 35 de cada 100 prs y esto es porque no fueron evaluados correctamente antes de pasar.

Métrica DORA:
Con la intervención se espera mover el Lead time para cambios ya que reduciendo tiempos de ejecución con el caché de dependencias, reducimos el lead time.
Las 2 métricas alcanzables sin despliegue son Frecuencia de despliegue y Lead time para cambios, ya que como se describen las otras 2 (Tasa falla de cambios y Tiempo restauración de servicio) que ambas miden estabilidad, se miden con los despliegues, y al no tenerlo ahora, no son alcanzables.

Se va a medir el tiempo de la validación, al reducir este podremos sustentar que la métrica se movió.
