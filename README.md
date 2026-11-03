# M27 · ¿Tomografía urgente por sospecha de ACV? · Neurología y Salud.

> **Hackatón IIA · UADE · 3.4.257.** L

**Integrantes:** Milena Iris Mastronardi · Tomás Villamil
**IA declarada:** Chat GPT
**Pista:** Neurona
**Publicación en LinkedIn:** <link>

---

## 1. Formulación y PEAS

**La pregunta:**
Para cada caso, ¿el paciente está cursando un ACV?

**SÍ** significa que el paciente está cursando un accidente cerebrovascular (ACV) y requiere una derivación a tomografía urgente.

**NO** significa que el paciente no está cursando un ACV y continúa con el triage habitual.

| PEAS | Su sistema |
|---|---|
| Medida de rendimiento | Identificar correctamente a los pacientes que están cursando un ACV, priorizando la detección de los casos reales y minimizando los falsos negativos. También se busca evitar una cantidad excesiva de falsos positivos que generen estudios innecesarios y ocupen recursos hospitalarios |
| Entorno |Guardia de un hospital. Incluye pacientes que presentan posibles síntomas neurológicos, profesionales de salud, el sistema informático de triage y la disponibilidad del tomógrafo.|
| Actuadores | Marcar al paciente como SÍ (tomografía urgente) o NO (sigue el triage habitual) y avisar al médico de guardia para que lo confirme. |
| Sensores | Asimetría facial (escala 0 a 3), debilidad en un brazo (escala 0 a 4) y dificultad para hablar (escala 0 a 3).|

**Objetivo y métrica.** 

Objetivo: Identificar oportunamente a los pacientes que están cursando un ACV para que puedan recibir una derivación urgente a tomografía y evitar que pierdan la ventana de tratamiento. Se busca priorizar la detección de los casos reales, reduciendo principalmente los falsos negativos, sin generar una cantidad excesiva de derivaciones innecesarias.

Métrica: La métrica principal es la sensibilidad (VP / (VP + FN)), porque un falso negativo es el error más costoso según la ficha. . Como control usamos la especificidad (VN / (VN + FP)),  dice qué parte de los pacientes sin ACV no se manda a tomografía, para no ocupar el tomógrafo de más. También utilizamos precisión (VP / (VP + FP), para ver de los que el sistema marca que si y se hicieron la tomografía, cuantos tenían una ACV.

La métrica y el objetivo no son lo mismo pero tienen una relación de causalidad entre sí, porque el objetivo define qué queremos lograr y la métrica nos permite medir si estamos alcanzándolo. En nuestro caso, el objetivo es detectar la mayor cantidad posible de pacientes que estén cursando un ACV, priorizando evitar los falsos negativos, y la sensibilidad es la métrica principal que utilizamos para evaluar si el sistema cumple con ese objetivo.


## 2. El sistema

**Cómo decide.**
El sistema utiliza una neurona artificial simple, también llamada perceptrón, para realizar una clasificación binaria: SÍ o NO.

Elegimos esta alternativa porque nuestro problema consiste en clasificar casos en dos categorías a partir de tres variables numéricas. El perceptrón permite aprender de los ejemplos de entrenamiento y utilizar lo aprendido para clasificar nuevos casos.

La neurona recibe tres variables de entrada y le asigna un peso a cada una, que representa su importancia dentro del cálculo. Luego multiplica cada valor por su peso, suma los resultados y agrega un sesgo (bias).

**Variables que usa y por qué.**
n=3F+B+3H-10
Usa las tres variables de la ficha porque están disponibles antes de la decisión y describen signos observados en el paciente. No descarta ninguna. Los pesos \(3\), \(1\) y \(3\) son una elección para estos datos inventados.

- \(F\): asimetría facial.
- \(B\): debilidad en un brazo.
- \(H\): dificultad para hablar.
- Pesos: 3, 1 y 3.
- Sesgo: −10.
- Si \(n\geq0\), predice SÍ; si \(n<0\), predice NO.

**Cómo se corre en vivo.**
En colab, para probar un caso nuevo, podes modificar los valores de Asimetría facial (escala 0 a 3), Debilidad en un brazo (escala 0 a 4), Dificultad para hablar (escala 0 a 3), así la neurona predice el resultado.
**Dos casos de ejemplo.**

| Caso inventado | Valores | Salida del sistema |
|---|---|---|
| A | 2, 2, 2 (3·2 + 1·2 + 3·2 + (-10) = 4)  | SÍ |
| B | 1, 2, 1	(3·1 + 1·2 + 3·1 + (-10) = -2) | NO |

## 3. Medición

**Entrenamiento (casos 1–14)**

| | Predice SÍ | Predice NO |
|---|---|---|
| **Real SÍ** | VP = 5 | FN = 1 |
| **Real NO** | FP = 2 | VN = 6 |

**Prueba (casos 15–20)**

| | Predice SÍ | Predice NO |
|---|---|---|
| **Real SÍ** | VP = 4 | FN = 0 |
| **Real NO** | FP = 1 | VN = 1 |

| Métrica | Entrenamiento | Prueba |
|---|---|---|
| Exactitud | 78.6% | 83.3% |
| Precisión | 71.4% | 80.0% |
| Sensibilidad | 83.3% | 100.00% |
| Especificidad | 75.0% | 50.0% |

**Entrenamiento contra prueba.**
Si, hay diferencia entre entrenamiento y prueba ya que durante la prueba suben todas las métricas en comparación al entrenamiento, excepto la especificidad, porque, de los 2 pacientes que no presentan ACV, uno fue una falsa alarma.
Al tener solamente 6 casos de prueba esto hace que, como solo hay un mal resultado, la especificidad baja un 25%. Por lo tanto, no podemos afirmar que exista una mejor generalización ni descartar un posible sobreajuste con una muestra tan pequeña.

## 4. El error más caro
El falso negativo es el error más grave, porque implica no detectar a un paciente que realmente está cursando un ACV, lo que podría retrasar su atención y hacerle perder la ventana de tratamiento. En cambio, un falso positivo puede generar una tomografía innecesaria y ocupar recursos hospitalarios.

Para reducir los falsos negativos, priorizamos la sensibilidad como métrica principal y ajustamos la neurona para favorecer la detección de los casos positivos, aceptando la posibilidad de tener algunos falsos positivos.

## 5. ¿Comparado contra qué?

**Línea base trivial.**

Como línea base trivial, consideramos un sistema que responde siempre NO, es decir, que clasifica a todos los pacientes como si no estuvieran cursando un ACV.

En nuestros 14 casos de entrenamiento, esta estrategia habría acertado 8 de 14 casos, obteniendo una exactitud del 57,14 %. Sin embargo, su sensibilidad sería del 0 %, porque no detectaría ninguno de los pacientes que realmente presentan un ACV.

Nuestro perceptrón obtuvo una exactitud del 78,6 % y una sensibilidad del 83,3 % en entrenamiento, por lo que supera a la línea base y resulta más adecuado para nuestro objetivo de detectar los casos positivos.

| Checklist (Clase 6) | Respuesta |
|---|---|
| ¿Qué datos? | 20 casos inventados, con tres variables: asimetría facial, debilidad en un brazo y dificultad para hablar. Se utilizaron 14 para entrenamiento y 6 para prueba. |
| ¿Qué métrica? | Sensibilidad como métrica principal, y exactitud, precisión y especificidad como métricas complementarias. |
| ¿Comparado contra qué? | Una línea base trivial que siempre responde NO y nuestro perceptrón. |
| ¿Quién lo midió? | Nosotros, como integrantes del equipo, mediante matrices de confusión y el cálculo de las métricas en entrenamiento y prueba. |

## 6. Riesgo: el camino completo

Datos: El sistema recibe tres síntomas del paciente: asimetría facial, debilidad en un brazo y dificultad para hablar. Un riesgo es que los valores ingresados sean incorrectos o no representen adecuadamente los síntomas reales.

Modelo: La neurona procesa esos datos utilizando los pesos y el sesgo obtenidos durante el entrenamiento. Como fue entrenada con pocos casos inventados, puede no generalizar correctamente a situaciones reales.

Decisión: El sistema clasifica al paciente como SÍ o NO. El principal riesgo es un falso negativo: que clasifique como NO a un paciente que realmente está cursando un ACV.

Consecuencia: Esto podría retrasar la derivación a tomografía y afectar las posibilidades de recibir un tratamiento oportuno.

Otro riesgo a considerar sería la posibilidad de que las falsas alarmas terminen en demoras al sistema y perder recursos.

Por estos motivos, el sistema debe considerarse un ejercicio académico y no una herramienta de diagnóstico médico ni un reemplazo del criterio profesional.

## 7. Bitácora

**Uso de la IA**

| # | Qué le pedimos | Qué aceptamos | Qué rechazamos y por qué |
|---|---|---|---|
| 1 | Una guía sobre el PEAS | Aceptamos parcialmente la respuesta de la IA de Mastronardi tras compararlas| Rechazamos las clasificaciones del entorno y Actuators, adoptamos la versión de Villamil al definir con más precisión lo que debe hacer la neurona|
| 2 | Métrica y Objetivo | Aceptamos el objetivo de Mastronardi porque se alinea con las bases de un proyecto de salud, también la explicación de la relación entre ambos conceptos, al alinearse con la idea de la cátedra | Rechazamos métrica Matronardi y usamos la de Villamil porque hace énfasis en el peso del falso negativo| 
| 3 | Formula de la neurona a partir de los casos de entrenamiento | La versión de Villamil porque los parámetros eran más pequeños y nos resultó más cómodo | La versión de Mastronardi porque ambas tenian la misma sensibilidad, la cual es la métrica más importante en nuestro caso. |
| 4 | Realizar la planilla de los casos de entrenamiento con la fórmula, la cual completó los resultados a partir de las cuentas | El código para Colab al ejecutarse correctamente y aportarnos las métricas necesarias. Lo usamos como primera prueba o intento. | Rechazamos la primer versión que nos dió porque las métricas estaban demasiado sesgadas para la exactitud, se ajustaba demasiado al conjunto de datos que proporcionamos y nos dió 100% en cada métrica al entrenar y probar los datos. No es aceptable.

**Corridas contra el evaluador**

| Corrida | Nota | Agujero que encontró | Qué cambiamos |
|---|---|---|---|
| 1 | 8 |	Fórmulas inconsistentes, falta responder sobre generalización y no se justificó qué cambio permitiría detectar el caso 6.	|Unificamos la fórmula, corregimos la precisión, completamos la sección 3 y analizamos el efecto de modificar el umbral, pero no lo hicimos porque priorizamos mantener el modelo original y evitar optimizarlo solamente para los datos de entrenamiento
| 2 | 9 | Solamente nos indica el mismo error de modificar el umbral. Además, no vió la corrección del Colab porque no la cargamos | No cambiamos nada porque lo volvimos a subir.
| 3 | 9 | Nos señala que no actualizamos la corrida 2 porque solo subimos el Colab ahora | Completamos la bitácora.
