# Fase A — Los 3 hallazgos más importantes del análisis exploratorio

**Dataset:** siniestralidad vial reportada en Santiago de Cali, 1-ene-2016 a 2-nov-2024 (371.000 reportes).

**Pregunta de negocio:** ¿qué características de un siniestro reportado permiten anticipar su gravedad, para priorizar
el despacho de agentes y ambulancias y focalizar la prevención?

**Detalle completo:** `entrega/fase_a_eda.ipynb` (15 gráficos con su insight).

---

## Hallazgo 1 — Motociclistas y ciclistas concentran la gravedad

| Siniestros que involucran… | Con lesionados | Con fallecidos | Casos |
|---|---|---|---|
| Bicicleta | **60,3%** | **3,2%** | 1.307 |
| Motocicleta | **42,7%** | **1,7%** | 68.238 |
| Transporte público | 30,4% | 1,0% | 13.411 |
| Vehículo de carga | 13,7% | 1,5% | 10.483 |
| Solo autos/camionetas | 11,5% | 0,3% | 60.525 |

Un siniestro con moto es **5 veces más letal** que uno entre carros, y las motos están presentes en el 46% de los
siniestros con información de vehículos. Automóviles y motos suman el 84% de todos los vehículos implicados (Pareto).

**Qué significa para el negocio:** las campañas, la infraestructura (carriles, cruces) y los controles con mayor impacto
en vidas son los dirigidos a motociclistas y ciclistas.

**Qué significa para el proyecto:** el tipo de vehículo es lo que más se asocia con la gravedad (Spearman +0,16 para
motos y −0,23 para autos; las variables de calendario ≈ 0). Pero ojo con la **fuga de datos**: el dato de vehículos falta
en el 81% de los reportes "Negativo" y solo en el 16–19% de los siniestros con daños o lesionados, porque se registra al
confirmar el caso. Sirve para explicar la gravedad entre siniestros confirmados, no para predecir si un reporte será
"Negativo".

## Hallazgo 2 — Dos de cada tres reportes no terminan en un siniestro confirmado

El **66,3% de los reportes son "Negativo"** (falsa alarma, los implicados no quieren intervención o ya se retiraron),
19,9% son solo daños, 12,8% con lesionados y apenas **0,9% con fallecidos** (3.360 casos).

**Qué significa para el negocio:** la mayor parte del esfuerzo de despacho se va en reportes que no requieren actuación;
anticipar cuáles reportes son graves permitiría priorizar agentes y ambulancias.

**Qué significa para el proyecto:** la variable objetivo está muy desbalanceada. En la Fase C se usará `train_test_split`
estratificado y se compararán los modelos con Recall y F1 por clase, no solo con Accuracy (un modelo que diga siempre
"Negativo" acertaría el 66% sin servir para nada).

## Hallazgo 3 — El sistema de registro cambió con el tiempo; menos reportes no es una ciudad más segura

- El volumen mensual cae de ≈5.000 a ≈2.500 reportes en marzo de 2020 (COVID-19) y nunca se recupera; en 2024 son ≈1.200
  al mes. El Paro Nacional (30-abr a 4-may-2021) deja días con 1 a 5 reportes.
- Los siniestros "solo daños" pasan de 47 diarios (2019) a 1–2 diarios (2023–2024), coincidiendo con la norma que los
  propios agentes citan en los reportes (Ley 2251 de 2022) para que los implicados resuelvan entre ellos los choques sin heridos.
- En cambio, **los siniestros con fallecido se mantienen en ≈1 por día (360–450 al año) entre 2016 y 2023**.
- Los faltantes dependen del año: la dirección no se registró en 2018–2020, el medio de reporte solo existe desde 2021 y la
  clase de accidente solo está casi completa en 2024 (falta en el 87,7% del total).

**Qué significa para el negocio:** la caída de reportes refleja cambios en la forma de reportar, no una mejora equivalente
de la seguridad vial; el indicador confiable para seguir el problema son los fallecidos.

**Qué significa para el proyecto:** en la Fase B los faltantes deben tratarse según el periodo (no son aleatorios) y las
columnas casi vacías no se pueden imputar a ciegas; en la Fase C conviene incluir el año como variable y revisar el
desempeño del modelo en los años recientes.

---

**Otros hallazgos de apoyo:** el domingo tiene menos reportes pero la mayor proporción de víctimas (17,6%) y de fallecidos
(1,4%, el doble que un jueves), y el lunes —día de la mayoría de festivos— es el segundo más letal; el volcamiento (3,8%) y
el atropello (2,6%) son las clases de accidente más letales; dos de las "direcciones" con más reportes son clínicas, donde se
registra el caso cuando llega el lesionado.
