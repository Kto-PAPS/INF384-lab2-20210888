Defecto 1: instalación de dependencias sin usar el archivo de bloqueo
Archivo: pipeline.yml
Líneas: 24-27 y 55-58
El problema es que el pipeline vuelve a resolver las dependencias en lugar de instalar las versiones fijadas en el archivo de bloqueo. Esto hace que el mismo código pueda utilizar versiones distintas de dependencias en ejecuciones diferentes.
La garantía que se pierde es la reproducibilidad del build: un mismo commit ya no garantiza el mismo conjunto de dependencias.


Defecto 2: las dependencias no se almacenan en caché
Archivo: pipeline.yml
Líneas: 19-27 y 50-58
En "Preparar Python" e "Instalar dependencias" no se configura ningún mecanismo de caché para pip.
Por ello, en cada ejecución el runner debe volver a descargar e instalar las dependencias desde cero, incluso cuando estas no han cambiado.
La consecuencia principal es un mayor tiempo de ejecución del pipeline y una retroalimentación más lenta para el desarrollador.


Defecto 3: el análisis de SonarCloud no bloquea el pipeline si falla el Quality Gate
Archivo: pipeline.yml
Líneas: 32-40
El workflow ejecuta el análisis de SonarCloud, pero no espera ni verifica el resultado del Quality Gate.
La garantía que se pierde es que solo avance código que haya cumplido los criterios de calidad definidos. El análisis funciona como reporte, pero todavía no como control del pipeline.


Defecto 4: la publicación no depende de la validación ni está correctamente restringida y versionada
Archivo: pipeline.yml
Líneas: 42-68
El job "publicar" no declara una dependencia con el job "validar". Además, su condición permite ejecutarlo ante cualquier "push" o una ejecución manual, sin comprobar que la rama sea "main".
También publica el artefacto con el nombre genérico "paquete", por lo que no queda identificada la versión del producto.
Como consecuencia, el artefacto puede publicarse sin haber confirmado previamente que la validación fue exitosa, puede publicarse desde una rama no deseada y pierde trazabilidad respecto de la versión que representa.
La garantía perdida es que únicamente un artefacto validado, proveniente de "main" y correctamente versionado sea publicado.

El defecto que explica la duración:
El defecto que se relaciona directamente con la duración es la ausencia de caché de dependencias. En la línea base se midieron ejecuciones de 53 s, 1m 6s y 1m 7s, lo que da un promedio de 1m 2s.
Al no reutilizar las dependencias entre ejecuciones, parte del tiempo del pipeline se consume descargándolas nuevamente. Por ello, habilitar caché debería reducir el tiempo de retroalimentación del pipeline.


Value StreamMap: 
El defecto más relacionado con el caso Seguros Pacífico Sur es la ausencia de un Quality Gate efectivo.

Un Quality Gate permite detectar automáticamente problemas antes de publicar el artefacto y evita que cambios que no cumplen los criterios establecidos continúen en el flujo, atacando parte del retrabajo observado en el caso.

Métrica DORA:
Las dos métricas sobre las que esta intervención puede tener efecto sin realizar todavía un despliegue real son el Lead Time para cambios y, como efecto preventivo, la Tasa de fallas en cambios.
Elijo Lead Time para cambios, porque el uso del caché reduce el tiempo de retroalimentación del pipeline.


Proxy:
Utilizaré la duración total de la ejecución del pipeline.
La línea base es:
- Ejecución 1: 53 s
- Ejecución 2: 66 s
- Ejecución 3: 67 s
- Promedio: 62 s
Después de la intervención compararé este promedio con una ejecución posterior utilizando el caché.


------------------------------------
Parte 4

4.1 Medición posterior

El proxy definido fue la duración total de la ejecución del pipeline.
La línea base estuvo compuesta por ejecuciones de 53 s, 1m 6s y 1m 7s, con un promedio de 1m 2s.

Después de la intervención, la ejecución exitosa en "main" tomó aproximadamente 80 s.

Por lo tanto, la duración aumentó en aproximadamente 18 s respecto de la línea base:

(80 - 62) / 62 × 100 = 29 %

Esto representa un incremento aproximado de 29 %. Aunque se incorporó caché para las dependencias, el pipeline ahora también realiza una validación adicional del Quality Gate antes de permitir la publicación. Por ello, la mejora en la instalación de dependencias no necesariamente se traduce en una reducción del tiempo total del pipeline.

La intervención prioriza que el feedback sea confiable y que un artefacto que no cumpla las condiciones de calidad no continúe hacia la publicación.

4.2 Justificación de la versión

La versión declarada es 1.3.0.

El punto de partida del historial es el tag v1.2.0. Después de este tag se observan commits de corrección, como:

- fix(tarifas): redondear el costo por peso a dos decimales
- fix(validaciones): colapsar espacios repetidos en el nombre del cliente

También se encuentra el commit:
- feat(tarifas): agregar desglose de la tarifa calculada

Según versionamiento semántico, los cambios fix corresponden a correcciones compatibles, mientras que un feat incorpora nueva funcionalidad compatible y requiere incrementar la versión MINOR.

Por ello, al existir una nueva funcionalidad y no identificarse un cambio incompatible, corresponde pasar de 1.2.0 a 1.3.0.


4.3 Lo que no se resolvió

Una limitación pendiente es que el pipeline termina con la publicación del artefacto en GitHub Actions, pero todavía no realiza un despliegue real hacia un ambiente de ejecución.
Esto limita la medición del flujo completo hasta producción y no permite obtener directamente métricas DORA relacionadas con despliegues e incidentes reales.
Para resolverlo sería necesario incorporar posteriormente una etapa de despliegue hacia un ambiente, mantener el mismo artefacto validado y registrar los despliegues, sus resultados y los tiempos de recuperación en caso de falla.

### 4.4 Declaración de uso de IA generativa

Se utilizó ChatGPT como apoyo para revisar la configuración del pipeline, contrastar conceptos técnicos y mejorar la claridad de algunas respuestas. La ejecución del laboratorio, revisión de resultados, selección de evidencias y sustentación de las decisiones fueron realizadas y verificadas por mi.
