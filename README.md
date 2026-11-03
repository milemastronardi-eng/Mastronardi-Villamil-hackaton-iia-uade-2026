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

Métrica: La métrica principal es la sensibilidad (VP / (VP + FN)), porque un falso negativo es el error más costoso según la ficha. . Como control usamos la especificidad (VN / (VN + FP)),  dice qué parte de los pacientes sin ACV no se manda a tomografía, para no ocupar el tomógrafo de más. También utilizamos precisión (VP / (VP + FN), para ver de los que el sistema marca que si y se hicieron la tomografía, cuantos tenían una ACV.

La métrica y el objetivo no son lo mismo pero tienen una relación de causalidad entre sí, porque el objetivo define qué queremos lograr y la métrica nos permite medir si estamos alcanzándolo. En nuestro caso, el objetivo es detectar la mayor cantidad posible de pacientes que estén cursando un ACV, priorizando evitar los falsos negativos, y la sensibilidad es la métrica principal que utilizamos para evaluar si el sistema cumple con ese objetivo.


## 2. El sistema

**Cómo decide.**
El sistema utiliza una neurona artificial simple, también llamada perceptrón, para realizar una clasificación binaria: SÍ o NO.

Elegimos esta alternativa porque nuestro problema consiste en clasificar casos en dos categorías a partir de tres variables numéricas. El perceptrón permite aprender de los ejemplos de entrenamiento y utilizar lo aprendido para clasificar nuevos casos.

La neurona recibe tres variables de entrada y le asigna un peso a cada una, que representa su importancia dentro del cálculo. Luego multiplica cada valor por su peso, suma los resultados y agrega un sesgo (bias).

**Variables que usa y por qué.**
n=3F+B+3H-10
Usa las tres variables de la ficha porque están disponibles antes de la decisión y describen signos observados en el paciente. No descarta ninguna. Los pesos \(3\), \(1\) y \(3\) son una elección para estos datos inventados.

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

**Entrenamiento contra prueba.** <¿Hay diferencia? ¿Por qué? ¿Sobreajuste o generalización?>

## 4. El error más caro

<¿Es peor el falso positivo o el falso negativo en su problema? ¿Por qué, según la ficha? ¿Qué cambiaron en el sistema para cometer menos ese error?>

## 5. ¿Comparado contra qué?

**Línea base trivial.** <Qué resultado saca un sistema que responde siempre lo mismo, y cómo se compara el suyo.>

| Checklist (Clase 6) | Respuesta |
|---|---|
| ¿Qué datos? | <> |
| ¿Qué métrica? | <> |
| ¿Comparado contra qué? | <> |
| ¿Quién lo midió? | <> |

## 6. Riesgo: el camino completo

<Datos → modelo → decisión → consecuencia aplicado a su caso. Al menos un riesgo concreto.>

## 7. Bitácora

**Uso de la IA**

| # | Qué le pedimos | Qué aceptamos | Qué rechazamos y por qué |
|---|---|---|---|
| 1 | Una guía sobre el PEAS | Aceptamos parcialmente la respuesta de la IA de Mastronardi tras compararlas| Rechazamos las clasificaciones del entorno y Actuators, adoptamos la versión de Villamil al definir con más precisión lo que debe hacer la neurona|
| 2 | Métrica y Objetivo | Aceptamos el objetivo de Mastronardi porque se alinea con las bases de un proyecto de salud, también la explicación de la relación entre ambos conceptos, al alinearse con la idea de la cátedra | Rechazamos métrica Matronardi y usamos la de Villamil porque hace énfasis en el peso del falso negativo| 
| 3 | Crear el sistema | 

**Corridas contra el evaluador**

| Corrida | Nota | Agujero que encontró | Qué cambiamos |
|---|---|---|---|
| 1 | <> | <> | <> |
