# Fase C — Análisis de resultados y justificación del modelo final

**Proyecto 2 y 3 · Grupo DatosSinCiencia**  
**Dataset:** reportes de siniestralidad vial en Cali, 2016–2024.

## 1. Objetivo y preparación

Se compararon tres modelos de clasificación para estimar la gravedad registrada de los reportes: Negativo, Solo daños, Con lesionado y Con fallecido. Adicionalmente, se aplicó K-Means para identificar segmentos con características similares, sin utilizar la gravedad como entrada.

Se utilizó `datos_limpios.parquet`, generado en la Fase B. Se conservó la partición estratificada creada mediante `train_test_split`: 296.796 reportes de entrenamiento y 74.200 de prueba. Las imputaciones, transformaciones y escaladores se ajustaron únicamente con entrenamiento.

Los clasificadores utilizaron las mismas 20 variables relacionadas con vehículos, fecha y medio de reporte. Se excluyeron identificadores, las dos representaciones de la variable objetivo, las variables de clase de accidente, las de tipo de vía y el indicador de vehículos faltantes. Estas últimas se excluyeron por su posible relación con la confirmación y el registro del resultado.

El ejercicio clasifica registros históricos con información disponible en el dataset; no demuestra capacidad para anticipar accidentes o su gravedad antes de ocurrir.

## 2. Modelos y criterios de evaluación

Se entrenaron regresión logística, árbol de decisión y Random Forest. El árbol utilizó profundidad máxima de 8; Random Forest, 200 árboles con profundidad máxima de 8. Los tres emplearon ponderación de clases mediante `class_weight="balanced"` y semilla 42. Las configuraciones se fijaron antes de evaluar.

La regresión logística se incluyó como modelo sencillo de comparación; el árbol permite representar reglas e interacciones no lineales; y Random Forest combina árboles para comparar un ensamble con un clasificador individual. Así se contrastan tres enfoques estudiados en las preclases sobre las mismas variables y particiones.

La profundidad máxima de 8 y los 200 árboles de Random Forest se tomaron como configuraciones iniciales de los ejemplos de clase. Limitar la profundidad restringe la complejidad, pero no demuestra por sí solo que se haya evitado el sobreajuste. No se realizó una búsqueda de hiperparámetros.

La ponderación de clases aumenta el peso de las categorías minoritarias durante el entrenamiento; sin embargo, no garantiza mejores resultados y puede aumentar las falsas alarmas. No se evaluó una configuración equivalente sin ponderación para medir ese efecto.

La clase Negativo representa el 66,34 % de los reportes y Con fallecido apenas el 0,91 %. Por este desbalance, se utilizó F1 macro como criterio principal de comparación: combina Precision y Recall y asigna el mismo peso a cada categoría. También se reportaron Accuracy, Precision macro, Recall macro, F1 ponderado, métricas por clase y matrices de confusión.

Todos los clasificadores se evaluaron sobre las mismas filas de prueba.

## 3. Comparación de resultados

| Modelo | Accuracy | Precision macro | Recall macro | F1 macro | F1 ponderado |
|---|---:|---:|---:|---:|---:|
| Regresión logística | 0,7243 | 0,5107 | 0,6369 | 0,5230 | 0,7444 |
| Árbol de decisión | 0,7270 | 0,5164 | 0,6394 | 0,5287 | 0,7469 |
| Random Forest | 0,7256 | 0,5182 | 0,6448 | 0,5303 | 0,7458 |

Como referencia, predecir siempre Negativo alcanza un Accuracy de 0,6634 y un F1 macro de 0,1994. Los tres modelos superan esta referencia en ambos indicadores. Random Forest mejora el Accuracy en aproximadamente 6,22 puntos porcentuales y alcanza un F1 macro de 0,5303.

Las diferencias entre los modelos son pequeñas. El árbol obtiene el mayor Accuracy y Random Forest el mayor F1 macro. Estos resultados, obtenidos con una sola partición, no permiten afirmar una superioridad consistente en otras muestras.

### Desempeño en reportes con fallecidos

| Modelo | Precision | Recall | F1 |
|---|---:|---:|---:|
| Regresión logística | 0,0981 | 0,6384 | 0,1701 |
| Árbol de decisión | 0,1051 | 0,6146 | 0,1795 |
| Random Forest | 0,1032 | 0,6280 | 0,1773 |

La regresión logística identifica la mayor proporción de reportes con fallecidos, mientras que el árbol alcanza la mayor precisión y F1 para esta clase. Ningún modelo domina en todos los criterios.

Random Forest identifica 422 de los 672 reportes con fallecidos y deja 250 clasificados en otras categorías. Además, asigna incorrectamente la categoría Con fallecido a 3.666 reportes. Su precisión del 10,32 % implica que aproximadamente una de cada diez predicciones de esta categoría es correcta.

## 4. Justificación del modelo seleccionado

Se selecciona **Random Forest entre las configuraciones evaluadas** porque presenta el mayor F1 macro, criterio principal utilizado para comparar el desempeño entre las cuatro clases. También obtiene el mayor Recall macro y el mayor F1 para Con lesionado.

La decisión tiene límites: su ventaja en F1 macro frente al árbol es de solo 0,0016 y no obtiene el mejor resultado específico para fallecidos. Si se priorizara la simplicidad y la interpretación de reglas, el árbol sería una alternativa razonable.

El modelo seleccionado sirve como resultado experimental del proyecto. Su baja precisión para fallecidos y la cantidad de casos no identificados no justifican utilizarlo como único criterio para tomar decisiones sobre reportes individuales.

## 5. Segmentación mediante K-Means

Se utilizaron las mismas 20 variables predictoras, sin incluir la gravedad, para agrupar reportes con características similares. K-Means se eligió por ser una técnica estudiada en la preclase de aprendizaje no supervisado y permitir describir los segmentos mediante sus perfiles medios.

Se compararon configuraciones de 2 a 10 grupos sobre una muestra reproducible de 10.000 reportes de entrenamiento. Silhouette se calculó sobre 2.000 de ellos. K = 6 obtuvo el mayor Silhouette, 0,4334, y el menor Davies-Bouldin, 0,6672, dentro del rango explorado.

Silhouette evalúa la cohesión y separación de los grupos, por lo que se prefieren valores más altos. Davies-Bouldin compara la dispersión interna con la separación entre grupos, por lo que se prefieren valores más bajos. Ambos indicadores coincidieron en favorecer seis segmentos en esta exploración.

Posteriormente, se entrenó K-Means con seis grupos sobre todo el conjunto de entrenamiento. Los reportes de prueba se asignaron a los centros aprendidos, sin reajustarlos. Sobre una muestra de 2.000 reportes de prueba se obtuvo:

| Indicador | Resultado |
|---|---:|
| Silhouette | 0,4261 |
| Davies-Bouldin | 0,6779 |

Estas métricas describen la estructura del agrupamiento, no la capacidad de predecir la gravedad.

Los perfiles medios permiten describir los segmentos de la siguiente manera:

| Cluster | Perfil observado | Reportes de entrenamiento | Reportes de prueba |
|---|---|---:|---:|
| 0 | Mayor valor relativo de automóviles; posible mezcla con registros imputados | 225.919 | 56.486 |
| 1 | Destacado en bicicletas | 1.050 | 251 |
| 2 | Destacado en transporte público | 10.155 | 2.526 |
| 3 | Destacado en vehículos de carga | 8.330 | 2.078 |
| 4 | Destacado en motocicletas | 50.543 | 12.660 |
| 5 | Destacado en otros vehículos | 799 | 199 |

Las descripciones se basan en medias de variables transformadas y estandarizadas, no en cantidades originales de vehículos. Los segmentos pueden contener combinaciones de distintos actores viales; sus nombres señalan características destacadas, no categorías exclusivas.

El grupo 0 concentra aproximadamente el 76 % de los reportes. Los segmentos minoritarios tienen pocos representantes en la muestra utilizada para las métricas: seis reportes del grupo 1 y tres del grupo 5. Por ello, las métricas globales ofrecen información limitada sobre estos grupos.

La proyección PCA de dos componentes conserva el 32,35 % de la varianza. Se utiliza para visualizar los segmentos, pero no representa toda la separación existente en las 20 variables. Los identificadores de cluster no expresan niveles de gravedad o riesgo.

La segmentación permite organizar el análisis por perfiles de vehículos, pero no demuestra que un grupo tenga mayor riesgo de accidente que otro.

## 6. Limitaciones y posibles mejoras

Los resultados corresponden a una partición aleatoria de registros históricos y no garantizan desempeño en años futuros. Los cambios temporales del registro y la elevada proporción de datos imputados pueden influir tanto en la clasificación como en la segmentación.

Aunque se excluyeron variables que podían revelar el resultado, los datos de vehículos también pueden haberse obtenido durante la confirmación del reporte. Antes de un uso operativo, debe comprobarse qué información existe realmente al momento de clasificar.

La selección del modelo se basó en la comparación del conjunto de prueba solicitada en el proyecto. Como mejora, convendría seleccionar modelos y configuraciones mediante validación dentro de entrenamiento y reservar una prueba independiente para la evaluación final.

No se realizó una búsqueda de hiperparámetros ni una comparación entre configuraciones con y sin ponderación de clases. Estas evaluaciones permitirían estudiar si es posible mejorar el equilibrio entre identificar casos minoritarios y reducir falsas alarmas.

También se propone revisar las métricas por año, estudiar la estabilidad del agrupamiento con otras muestras y semillas, y evaluar el efecto de los patrones de información faltante. Estas comprobaciones permitirían determinar si los resultados reflejan características de los reportes o particularidades del sistema de registro.

## 7. Conclusión

Los tres clasificadores superan la referencia de predecir siempre Negativo. Random Forest se selecciona por su mayor F1 macro, con una ventaja pequeña frente al árbol y limitaciones importantes en la clasificación de fallecidos.

K-Means complementa el análisis con seis perfiles asociados principalmente a las variables de vehículos. La clasificación y la segmentación aportan herramientas para estudiar los registros, pero sus resultados deben interpretarse considerando el desbalance, la imputación y los cambios en la captura de información.