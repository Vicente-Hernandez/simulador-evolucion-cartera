# 🚀 Simulador de Evolución de Cartera de Créditos (Fase 2)

Este repositorio contiene la segunda fase de un proyecto de simulación de datos financieros, diseñado para dar vida a una cartera de créditos automotrices. Mientras que la Fase 1 (`simulador-credito-automotriz`) se enfoca en crear la "fotografía" del momento de la originación de cada crédito, este proyecto se encarga de crear la "película": la evolución y el comportamiento de cada cliente a lo largo del tiempo.

El objetivo de este motor de simulación es tomar un dataset de créditos recién aprobados y proyectar su ciclo de vida mes a mes, generando un rico historial de pagos, morosidad, recuperaciones y, finalmente, la decisión de renovación.

---
## 🔗 Relación con la Fase 1

Este simulador es un consumidor de los datos generados por el proyecto de **Fase 1**. Su punto de partida es el archivo `dataset.xlsx` (específicamente la pestaña `Solo_Aprobados`) que contiene la cartera de créditos aprobados y financiados.

El propósito es tomar las características iniciales de cada cliente y crédito (su arquetipo, score, monto, plazo, etc.) y usarlas como base para una simulación de comportamiento mensual y de fin de ciclo.

---
## 🧠 La Lógica de la Simulación: Un Modelo Probabilístico

A diferencia de un modelo determinista, la vida financiera de un cliente no es predecible. Por ello, el corazón de este simulador es un **motor probabilístico** que calcula el estado de un cliente cada mes basándose en una combinación de su perfil, su historial y factores externos.

### Características Clave del Modelo

#### 1. Modelo de Transición de Estados
Cada mes, un cliente puede estar en uno de tres estados: **Al Día**, **Atrasado** o **En Default**. La simulación calcula la probabilidad de que un cliente transicione de un estado a otro.

#### 2. El Riesgo Inherente como Guía
La columna `FUTURO_DEFAULT` generada en la Fase 1 no es una sentencia, sino un poderoso **influenciador**. La probabilidad mensual de que un cliente tenga problemas es significativamente mayor si esta bandera es `True`.

#### 3. "Cisnes Negros" y Recuperaciones
Para simular la aleatoriedad de la vida, el modelo incorpora dos mecánicas clave:
* **Shocks Aleatorios ("Cisnes Negros"):** Cada mes, cualquier cliente tiene una probabilidad muy pequeña de sufrir un evento catastrófico que lo fuerce a un estado de `Default`, simulando eventos como la pérdida de un empleo.
* **Mecanismos de Recuperación:** Un cliente que está `Atrasado` o `En Default` tiene una probabilidad de recuperarse. Esta probabilidad es mayor para clientes con mejores perfiles (arquetipos y scores más altos).

#### 4. Influencia Macroeconómica
La **tasa de desempleo** del mes actúa como un factor de estrés sobre toda la cartera, afectando las probabilidades de pago de todos los clientes.

#### 5. Simulación de Fin de Ciclo y Renovación
Al finalizar el plazo de un crédito, el simulador activa una compleja lógica de negocio para determinar si el cliente renueva su vehículo:
* **Tasación del Vehículo:** Se calcula un valor de retoma realista, deduciendo el IVA del precio original, aplicando una depreciación anual (que es menor si el cliente realizó las **mantenciones en la marca**) y restando el margen del concesionario.
* **Cálculo de Equity:** Se determina el "pie disponible" para el cliente. Para créditos de tipo "Renovación", esto implica restar el monto del **cuotón final** del valor de retoma.
* **Árbol de Decisión:** El simulador replica la decisión del cliente basándose en su tipo de crédito, su comportamiento de pago histórico y su "solvencia" (definida por su arquetipo), decidiendo si el resultado final es **Renueva**, **No Renueva** o si queda **Inhabilitado para Renovar**.

---
## 🛠️ ¿Cómo Ejecutar el Simulador?

El script está diseñado para ser simple de usar:

1.  **Genera los Datos de Origen:** Ejecuta el simulador de **Fase 1** para producir el archivo `dataset.xlsx`.
2.  **Prepara el Entorno:** Coloca el archivo `dataset.xlsx` en el mismo directorio que el script de esta Fase 2.
3.  **Ejecuta el Script:** Corre el script de Python. Se encargará de leer el archivo de entrada, ejecutar la simulación completa y guardar los resultados.

---
## 📊 El Resultado: `dataset_evolucion.xlsx`

El archivo de salida es un completo historial de la cartera, presentado en dos pestañas para facilitar el análisis:

#### `Evolucion_Mensual` (La "Película"):
* Contiene el historial detallado, con una fila por cada cuota de cada crédito.
* **Columnas Clave:** `ID_CREDITO`, `NUMERO_CUOTA`, `FECHA_CUOTA`, `ESTADO_PAGO`, `DIAS_ATRASO_MES`, `DIAS_ATRASO_ACUMULADO`, `SALDO_PENDIENTE`.
* **Uso Ideal:** Análisis de series de tiempo, cálculo de tasas de morosidad (30, 60, 90 días), *behavioral scoring*.

#### `Estado_Final_Cartera` (La "Foto Final"):
* Contiene una única fila por cada crédito, mostrando un resumen de su estado y decisión al final del período.
* **Columnas Clave:** Datos originales del crédito (modelo, monto, etc.) cruzados con su resultado final como `ESTADO_PAGO_FINAL`, `DIAS_ATRASO_ACUMULADOS_TOTAL`, **`VALOR_RETOMA_VEHICULO`**, **`PIE_DISPONIBLE_RENOVACION`** y **`DECISION_FINAL`**.
* **Uso Ideal:** Reportes gerenciales, análisis de la salud general de la cartera, y modelos de propensión a la renovación.
