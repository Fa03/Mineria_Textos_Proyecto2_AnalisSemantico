## Uso de IA durante el proyecto (Gemini, Copilot y Claude)

Este documento registra, en un orden parecido al avance del trabajo, las consultas hechas a asistentes de inteligencia artificial durante el desarrollo del proyecto de minería de textos. Para cada caso se anota el motivo de la consulta y un resumen de la respuesta.

### 1. Archivos de data/raw eran agregados a Git pese a estar en .gitignore

***Prompt:***
Se consultó por qué los archivos ubicados en data/raw seguían siendo incluidos al ejecutar git add --all, a pesar de que la ruta había sido agregada al archivo .gitignore.

***Respuesta*** (resumida)
Se explicó que el problema podía deberse a dos causas: una regla incorrecta en el .gitignore o que los archivos ya estuvieran siendo rastreados por Git antes de crear la regla de exclusión.

***Aprendizaje obtenido:***
La lección que quedó es que .gitignore solo evita que Git agregue archivos nuevos, pero no elimina del control de versiones aquellos que ya fueron agregados previamente. La duda —creer que bastaba con escribir la regla para que el archivo desapareciera— se resolvió al entender que Git arrastra lo que ya rastreaba; así, el .gitignore dejó de verse como un interruptor y pasó a leerse como una regla que solo mira hacia adelante.

### 2. Problemas de edición de archivos Markdown en PyCharm

***Prompt:***
Se consultó por qué un archivo Markdown recién creado no permitía editar ni agregar texto ("por qué no puedo editar, agregar texto a ese archivo recién creado").

***Respuesta*** (resumida)
Se identificaron tres causas principales de que PyCharm impidiera editar el archivo: que estuviera en modo de solo lectura (por permisos del sistema o por el candado del IDE), que se estuviera viendo solo en la pestaña Preview en vez del Editor o el modo Split, o que la interfaz estuviera bloqueada por la indexación o por tener seleccionado el panel equivocado.

***Aprendizaje obtenido:***
Lo que este caso enseñó es que un IDE como PyCharm gestiona los archivos de documentación con sus propias reglas y no solo con las del sistema. La duda —pensar que el archivo estaba dañado o corrupto— se aclaró al revisar el modo de vista y los permisos; de ahí que ante un bloqueo convenga sospechar primero de la interfaz antes que del contenido.

### 3. Organización del trabajo con ramas y pull requests en GitHub

***Prompt:***
Se consultó cómo ordenar el desarrollo para no trabajar siempre sobre la rama principal, y para qué servían las ramas y los pull requests en un proyecto hecho por una sola persona.

***Respuesta*** (resumida)
Se explicó que cada etapa del trabajo puede vivir en su propia rama, que los pull requests permiten revisar y comentar los cambios antes de integrarlos, y que así el historial de `main` queda limpio y con merges claros. En el repositorio del proyecto esto se ve en ramas como `Datos_Muestreo`, `LimpiezaResenasEspanol`, `POS_Tagging` y `Análisis`, cada una integrada mediante un pull request numerado.

***Aprendizaje obtenido:***
Lo aprendido es que las ramas y los pull requests no son un trámite, sino una forma de aislar cada cambio, revisarlo y dejar un historial claro de las decisiones. La duda inicial —creer que trabajar solo hacía innecesario dividir el trabajo— dio paso a entender que un error en una etapa puede arrastrar a las demás, y que sin ramas no hay vuelta atrás ordenada; así, el repositorio dejó de verse como una carpeta de archivos y pasó a leerse como el historial de las decisiones.

### 4. Construcción del pipeline de datos con pandas

***Prompt:***
Se consultó cómo unir los dos datasets de IMDb (`IMDB_reviews.json` e `IMDB_movie_details.json`) sin perder registros, cómo parsear las fechas y obtener el año, cómo filtrar las reseñas por longitud, período y género, y cómo tomar una muestra equilibrada y repetible.

***Respuesta*** (resumida)
Se mostró el uso de `merge` sobre `movie_id`, de `pd.to_datetime` con `errors="coerce"`, de `dropna` para quitar las filas sin año o sin texto, del filtro `between` para la cantidad de palabras y del muestreo por grupos con `groupby` y una semilla fija para que el resultado fuera reproducible. También se propuso asignar un solo género por reseña, eligiendo el más raro entre los principales para equilibrar la muestra.

***Aprendizaje obtenido:***
El conocimiento que dejó este caso es que limpiar datos no es borrar filas malas, sino tomar decisiones: elegir un género principal por reseña, quedarse con el más raro para balancear y fijar una semilla para poder repetir el resultado.

### 5. Traducción de las reseñas al español con Transformers

***Prompt:***
Se consultó cómo traducir unas tres mil reseñas del inglés al español con un modelo preentrenado, por qué el proceso tardaba tanto y cómo hacerlo más rápido, más ordenado y reanudable si se interrumpía.

***Respuesta*** (resumida)
Se explicó el uso del modelo `Helsinki-NLP/opus-mt-en-es`, la cuantización dinámica a int8 en CPU (unas seis veces más rápida), el uso de `beam=1` en lugar del beam search por defecto, la agrupación de las oraciones por longitud y la traducción en lotes grandes en vez de reseña por reseña. También se recomendó guardar cada bloque de filas para poder reanudar, y normalizar los espacios antes de `sent_tokenize`, porque muchas reseñas venían pegadas ("actors.Love this").

***Aprendizaje obtenido:***
De esta etapa se desprende que la eficiencia no depende solo de la potencia del equipo, sino de cómo se ordena el trabajo: agrupar oraciones por longitud, traducir en lotes, cuantizar en CPU y guardar por bloques para reanudar.

### 6. Etiquetado gramatical con spaCy y NLTK

***Prompt:***
Se consultó cómo etiquetar cada palabra con spaCy y con NLTK, cómo traducir las etiquetas del corpus `cess_esp` al esquema UPOS y cómo medir en qué coinciden ambos etiquetadores.

***Respuesta*** (resumida)
Se propuso cargar el modelo `es_core_news_md` desactivando el componente de entidades, convertir las etiquetas EAGLES a UPOS, entrenar un `UnigramTagger` y un `BigramTagger` con respaldo sobre `cess_esp`, y crear una columna `coincide` para saber cuántas etiquetas eran iguales en los dos sistemas. La concordancia global resultó cercana al 83% al excluir la puntuación.

***Aprendizaje obtenido:***
La enseñanza es que comparar dos etiquetadores no busca un ganador, sino entender por qué difieren: esquemas de anotación distintos (`AUX` frente a `VERB`) y corpus de entrenamiento distintos explican la mayoría del desacuerdo.

### 7. Cálculo de las métricas morfosintácticas

***Prompt:***
Se consultó cómo convertir preguntas del estudio en números (si las comedias adjetivan más, si las reseñas negativas usan más intensificadores, si las reseñas recientes son más simples) y cómo comparar los resultados de NLTK con los de spaCy.

***Respuesta*** (resumida)
Se explicó el uso de métricas normalizadas por cada 100 palabras, la exclusión de la puntuación, el cálculo de la diversidad léxica sobre bloques de 100 lemas, la agrupación por género, año, polaridad y presencia de spoilers, la generación de figuras con `seaborn`, la matriz de confusión y la clasificación de las discrepancias por causa. También se propuso una caché de spaCy para no repetir el trabajo más costoso en cada ejecución.

***Aprendizaje obtenido:***
Lo que quedó claro es que una métrica es la traducción de una pregunta, y que la pregunta misma puede estar mal planteada: normalizar por 100 palabras, por ejemplo, es una decisión que cambia las comparaciones.

### 8. Limpieza final del corpus y configuración centralizada

***Prompt:***
Se consultó cómo dejar limpio el corpus ya traducido (etiquetas HTML, textos vacíos, duplicados y espacios repetidos) y cómo ordenar los parámetros del proyecto para no repetirlos por todo el código.

***Respuesta*** (resumida)
Se propuso un script de limpieza que elimina etiquetas HTML, quita textos vacíos y duplicados y normaliza los espacios, y un archivo de configuración que reúne rutas, filtros, umbrales y léxicos para que el resto del código los lea desde un solo lugar.

***Aprendizaje obtenido:***
Cada etapa produce sus propios desórdenes y que limpiar lo que parece ya listo también es parte del trabajo, del mismo modo que reunir los parámetros en un solo archivo evita que un cambio se pierda por el código.

### 9. Documentación del proyecto y revisión crítica

***Prompt:***
Se consultó cómo redactar el `README` para describir el objetivo, el pipeline y las métricas, y cómo revisar el proyecto para encontrar inconsistencias antes de la entrega.

***Respuesta*** (resumida)
Se ayudó a describir el flujo del pipeline, las decisiones de análisis y las limitaciones, y a listar pendientes: un `import` de `traduccion_1` que apunta a un archivo inexistente, la diferencia entre `processed_es.csv` y `resenas_es.csv`, un `.gitignore` que excluye `LimpiezaResenasEspanol.py` aunque el pipeline lo usa, dependencias de NLTK que no se declaran y opciones de traducción que ya no se utilizan.

***Aprendizaje obtenido:***
La conclusión que se extrae es que documentar es una de las formas más honestas de revisar, porque obliga a explicar lo hecho y a reconocer lo que quedó a medias.

bargo, lo que más marcó el aprendizaje no fueron los aciertos sino las dudas. Cada vez que un resultado no cuadraba con lo esperado, fue necesario preguntar, contrastar y, en más de una ocasión, cambiar de opinión: sobre las ramas, sobre la lentitud de un modelo, sobre lo que significaba el 83% de coincidencia entre etiquetadores, sobre cuándo conviene limpiar y sobre la propia forma de usar la inteligencia artificial. De ese recorrido queda la lección más valiosa: que las respuestas de una IA son un punto de partida y no una verdad cerrada, y que el conocimiento se construye al verificar, equivocarse y volver a pensar. La IA sirvió, en suma, para aprender a pensar mejor, que es, quizá, la faceta que resume todas las demás.