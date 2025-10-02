# 🚀 Simulador de Evolución de Cartera de Créditos (Fase 2)

Este repositorio contiene la segunda fase de un proyecto de simulación de datos financieros, diseñado para dar vida a una cartera de créditos automotrices. Mientras que la Fase 1 (`simulador-credito-automotriz`) se enfoca en crear la "fotografía" del momento de la originación de cada crédito, este proyecto se encarga de crear la "película": la evolución y el comportamiento de cada cliente a lo largo del tiempo.

El objetivo de este motor de simulación es tomar un dataset de créditos recién aprobados y proyectar su ciclo de vida mes a mes, generando un rico historial de pagos, morosidad y recuperaciones.

---
## 🔗 Relación con la Fase 1

Este simulador es un consumidor de los datos generados por el proyecto de **Fase 1**. Su punto de partida es el archivo `dataset.xlsx` (específicamente la pestaña `Solo_Aprobados`) que contiene la cartera de créditos aprobados y financiados.

El propósito es tomar las características iniciales de cada cliente y crédito (su arquetipo, score, monto, plazo, etc.) y usarlas como base para una simulación de comportamiento mensual.

---
## 🧠 La Lógica de la Simulación: Un Modelo Probabilístico

A diferencia de un modelo determinista, la vida financiera de un cliente no es predecible. Por ello, el corazón de este simulador es un **motor probabilístico** que calcula el estado de un cliente cada mes basándose en una combinación de su perfil, su historial y factores externos.

### Características Clave del Modelo

#### 1. Modelo de Transición de Estados
Cada mes, un cliente puede estar en uno de tres estados: `Al Día`, `Atrasado` o `En Default`. La simulación calcula la probabilidad de que un cliente transicione de un estado a otro.

#### 2. El Riesgo Inherente como Guía
La columna `FUTURO_DEFAULT` generada en la Fase 1 no es una sentencia, sino un poderoso **influenciador**. La probabilidad mensual de que un cliente tenga problemas es significativamente mayor si esta bandera es `True`, pero no es una garantía de impago.

#### 3. "Cisnes Negros" y Recuperaciones
Para simular la aleatoriedad de la vida, el modelo incorpora dos mecánicas clave:
* **Shocks Aleatorios ("Cisnes Negros"):** Cada mes, cada cliente tiene una probabilidad muy pequeña de sufrir un evento catastrófico que lo fuerce a un estado de `Default`, sin importar lo bueno que sea su perfil. Esto simula eventos como la pérdida de un empleo o un mal negocio.
* **Mecanismos de Recuperación:** Un cliente que está `Atrasado` o `En Default` tiene una probabilidad de recuperarse y volver al estado `Al Día`. Esta probabilidad es mayor para clientes con mejores perfiles (arquetipos y scores más altos), simulando una reestructuración de deuda o la estabilización de sus finanzas.

#### 4. Influencia Macroeconómica
La tasa de desempleo del mes actúa como un factor de estrés sobre toda la cartera, aumentando ligeramente la probabilidad de que todos los clientes experimenten dificultades de pago.

---
## 🛠️ ¿Cómo Ejecutar el Simulador?

El script está diseñado para ser simple de usar:

1.  **Genera los Datos de Origen:** Ejecuta el simulador de **Fase 1** para producir el archivo `dataset.xlsx`.
2.  **Prepara el Entorno:** Coloca el archivo `dataset.xlsx` en el mismo directorio que el script de esta Fase 2.
3.  **Ejecuta el Script:** Corre el script de Python. Se encargará de leer el archivo de entrada, ejecutar la simulación completa y guardar los resultados.

---
## 📊 El Resultado: `dataset_evolucion.xlsx`

El archivo de salida es un completo historial de la cartera, presentado en dos pestañas para facilitar el análisis:

1.  **`Evolucion_Mensual` (La "Película"):**
    * Contiene el historial detallado, con una fila por cada cuota de cada crédito.
    * **Columnas Clave:** `ID_CREDITO`, `NUMERO_CUOTA`, `FECHA_CUOTA`, `ESTADO_PAGO`, `DIAS_ATRASO_MES`, `DIAS_ATRASO_ACUMULADO`, `SALDO_PENDIENTE`.
    * **Uso Ideal:** Análisis de series de tiempo, cálculo de tasas de morosidad (30, 60, 90 días), `behavioral scoring`.

2.  **`Estado_Final_Cartera` (La "Foto Final"):**
    * Contiene una única fila por cada crédito, mostrando un resumen de su estado al final del período de simulación.
    * **Columnas Clave:** Datos originales del crédito (modelo, monto, etc.) cruzados con su resultado final (`ESTADO_PAGO_FINAL`, `SALDO_PENDIENTE_FINAL`, `DIAS_ATRASO_ACUMULADOS`).
    * **Uso Ideal:** Reportes gerenciales, análisis de la salud general de la cartera, validación de modelos de riesgo.

Este dataset de evolución permite explorar patrones de comportamiento, medir el impacto de factores macroeconómicos y, en última instancia, construir modelos predictivos de comportamiento de pago mucho más sofisticados.
