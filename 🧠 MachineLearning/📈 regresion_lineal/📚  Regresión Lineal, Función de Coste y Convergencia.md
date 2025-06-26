

## 1. Conceptos Fundamentales

### 1.1 **Regresión Lineal**

Es un modelo que busca encontrar una **recta** (o hiper plano en dimensiones superiores) que mejor se ajuste a los datos. La fórmula general es:

$$y = a + b \cdot x$$

- **`a`**: Intercepto (valor de `y` cuando `x = 0`).
- **`b`**: Pendiente (cambio en `y` por unidad de `x`).

Para múltiples variables, la fórmula se expande a:

$$y = \theta_0 + \theta_1 x_1 + \theta_2 x_2 + ... + \theta_n x_n$$

Que puede escribirse de forma matricial como:

$$y = \theta^T X$$

---

### 1.2 **Función de Coste (MSE - Error Cuadrático Medio)**

Mide el error promedio entre las predicciones del modelo (`ŷ`) y los valores reales (`y`).

#### **Versión 1: Con `1/(2m)`**

$$J(\theta) = \frac{1}{2m} \sum_{i=1}^{m} (ŷ_i - y_i)^2$$

- **Derivadas**:

$$\frac{\partial J}{\partial \theta_j} = \frac{1}{m} \sum_{i=1}^{m} (ŷ_i - y_i) \cdot x_{ij}$$

(Donde $x_{i0} = 1$ para el término independiente)

#### **Versión 2: Con `1/m`**

$$J(\theta) = \frac{1}{m} \sum_{i=1}^{m} (ŷ_i - y_i)^2$$

- **Derivadas**:

$$\frac{\partial J}{\partial \theta_j} = \frac{2}{m} \sum_{i=1}^{m} (ŷ_i - y_i) \cdot x_{ij}$$

> **Nota**: El factor 1/2 en la primera versión se usa para simplificar la derivada, eliminando el factor 2 que surge al derivar el término cuadrático.

---

### 1.3 **Convergencia con Gradiente Descendiente**

Proceso iterativo para ajustar los parámetros $\theta$ minimizando el coste:

#### **Actualización de Parámetros**:

$$\theta_j^{\text{nuevo}} = \theta_j^{\text{antiguo}} - \alpha \cdot \frac{\partial J}{\partial \theta_j}$$

- **`α`**: Tasa de aprendizaje.

**Visualización del Gradiente Descendiente:**

- La función de coste forma una superficie en el espacio
- Cada punto representa una combinación de parámetros
- El algoritmo "desciende" hacia el punto más bajo (mínimo)

#### **Tipos de Gradiente Descendiente**:

1. **Batch Gradient Descent**: Usa todos los datos para cada actualización
2. **Stochastic Gradient Descent (SGD)**: Usa un solo dato para cada actualización
3. **Mini-Batch Gradient Descent**: Usa un subconjunto de datos para cada actualización

---

## 2. Caso de Negocio: Predecir Ventas de Helados 🍦

### **2.1 Datos Históricos**

|Temperatura (°C)|Ventas (helados)|
|---|---|
|20|50|
|25|80|
|30|120|
|15|40|
|35|150|

---

### **2.2 Definir el Modelo Inicial**

Valores iniciales:

$$a = 10, \quad b = 3$$

Predicciones iniciales:

- Para 20°C: $ŷ = 10 + 3 \cdot 20 = 70$ (Error: 70 - 50 = 20)
- Para 25°C: $ŷ = 10 + 3 \cdot 25 = 85$ (Error: 85 - 80 = 5)
- Para 30°C: $ŷ = 10 + 3 \cdot 30 = 100$ (Error: 100 - 120 = -20)
- Para 15°C: $ŷ = 10 + 3 \cdot 15 = 55$ (Error: 55 - 40 = 15)
- Para 35°C: $ŷ = 10 + 3 \cdot 35 = 115$ (Error: 115 - 150 = -35)

---

### **2.3 Calcular el Coste (Versión `1/(2m)`)**

$$J(10, 3) = \frac{1}{2 \cdot 5} \left[(70-50)^2 + (85-80)^2 + (100-120)^2 + (55-40)^2 + (115-150)^2 \right]$$ $$= \frac{1}{10} \left[400 + 25 + 400 + 225 + 1225 \right]$$ $$= \frac{2275}{10} = 227.5$$

---

### **2.4 Calcular Derivadas Parciales (Versión `1/(2m)`)**

- **Derivada respecto a `a`**:

$$\frac{\partial J}{\partial a} = \frac{1}{5} \left[(70-50) + (85-80) + (100-120) + (55-40) + (115-150) \right]$$ $$= \frac{1}{5} \left[20 + 5 + (-20) + 15 + (-35) \right]$$ $$= \frac{1}{5} \cdot (-15) = -3$$

- **Derivada respecto a `b`** (corregida):

$$\frac{\partial J}{\partial b} = \frac{1}{5} \left[(70-50) \cdot 20 + (85-80) \cdot 25 + (100-120) \cdot 30 + (55-40) \cdot 15 + (115-150) \cdot 35 \right]$$ $$= \frac{1}{5} \left[20 \cdot 20 + 5 \cdot 25 + (-20) \cdot 30 + 15 \cdot 15 + (-35) \cdot 35 \right]$$ $$= \frac{1}{5} \left[400 + 125 - 600 + 225 - 1225 \right]$$ $$= \frac{1}{5} \cdot (-1075) = -215$$

---

### **2.5 Ajustar Parámetros**

Con tasa de aprendizaje `α = 0.01`:

$$a_{\text{nuevo}} = 10 - 0.01 \cdot (-3) = 10 + 0.03 = 10.03$$  
$$b_{\text{nuevo}} = 3 - 0.01 \cdot (-215) = 3 + 2.15 = 5.15$$

---

### **2.6 Segunda Iteración (Demostrativa)**

Con los nuevos parámetros $a = 10.03, b = 5.15$:

Predicciones actualizadas:

- Para 20°C: $ŷ = 10.03 + 5.15 \cdot 20 = 113.03$ (Error: 113.03 - 50 = 63.03)
- Para 25°C: $ŷ = 10.03 + 5.15 \cdot 25 = 138.78$ (Error: 138.78 - 80 = 58.78)
- Para 30°C: $ŷ = 10.03 + 5.15 \cdot 30 = 164.53$ (Error: 164.53 - 120 = 44.53)
- Para 15°C: $ŷ = 10.03 + 5.15 \cdot 15 = 87.28$ (Error: 87.28 - 40 = 47.28)
- Para 35°C: $ŷ = 10.03 + 5.15 \cdot 35 = 190.28$ (Error: 190.28 - 150 = 40.28)

Coste actualizado:

$$J(10.03, 5.15) = \frac{1}{10} \left[63.03^2 + 58.78^2 + 44.53^2 + 47.28^2 + 40.28^2 \right]$$ $$= \frac{1}{10} \cdot 13662.39 = 1366.24$$

> Observación: El coste aumentó de 227.5 a 1366.24. Esto significa que necesitamos ajustar la tasa de aprendizaje, ya que es demasiado grande y nos está haciendo "saltar" demasiado lejos.

---

### **2.7 Convergencia con Tasa de Aprendizaje Ajustada**

Con una tasa de aprendizaje menor, por ejemplo `α = 0.001`:

$$a_{\text{nuevo}} = 10 - 0.001 \cdot (-3) = 10.003$$  
$$b_{\text{nuevo}} = 3 - 0.001 \cdot (-215) = 3.215$$

Después de múltiples iteraciones (alrededor de 10,000 con `α = 0.001`), los parámetros convergen aproximadamente a:

$$a \approx -12.2, \quad b \approx 4.7$$

**Modelo Final**:

$$ŷ = -12.2 + 4.7 \cdot x$$

- **Predicción para 28°C**:

$$ŷ = -12.2 + 4.7 \cdot 28 \approx 119.4 \text{ helados}$$

---

## 3. Implementación en Python

```python
import numpy as np
import matplotlib.pyplot as plt

# Datos de ejemplo
X = np.array([20, 25, 30, 15, 35])  # Temperatura
y = np.array([50, 80, 120, 40, 150])  # Ventas

# Parámetros iniciales
a = 10
b = 3
alpha = 0.0001  # Tasa de aprendizaje
iterations = 50000

# Historiales para visualización
cost_history = []
a_history = []
b_history = []

# Gradiente descendiente
for i in range(iterations):
    # Cálculo de predicciones
    y_pred = a + b * X
    
    # Cálculo del coste
    cost = (1/(2*len(X))) * np.sum((y_pred - y)**2)
    cost_history.append(cost)
    
    # Cálculo de derivadas
    da = (1/len(X)) * np.sum(y_pred - y)
    db = (1/len(X)) * np.sum((y_pred - y) * X)
    
    # Actualización de parámetros
    a = a - alpha * da
    b = b - alpha * db
    
    # Guardar historiales
    a_history.append(a)
    b_history.append(b)

print(f"Parámetros finales: a = {a:.2f}, b = {b:.2f}")
print(f"Coste final: {cost_history[-1]:.2f}")
```

---

## 4. Visualización del Proceso de Convergencia

### 4.1 **Evolución del Coste**

La evolución del coste típicamente muestra:

- Disminución rápida al principio
- Desaceleración gradual
- Estabilización cuando se acerca al mínimo

### 4.2 **Evolución de los Parámetros**

Los parámetros suelen:

- Cambiar rápidamente al principio
- Luego ajustarse más finamente
- Eventualmente estabilizarse cuando el modelo converge

### 4.3 **Ajuste del Modelo Final**

Un buen ajuste del modelo final debería:

- Pasar cerca de la mayoría de los puntos
- No estar sesgado hacia ninguna región específica de los datos
- Capturar la tendencia general sin sobreajustarse a puntos específicos

---

## 5. Regresión Lineal Múltiple: Ampliando el Modelo

La regresión lineal múltiple extiende el concepto a múltiples variables predictoras:

$$y = \theta_0 + \theta_1 x_1 + \theta_2 x_2 + ... + \theta_n x_n$$

### 5.1 **Ejemplo: Predecir Precios de Casas**

Variables predictoras:

- Tamaño (m²)
- Número de habitaciones
- Antigüedad (años)

|Tamaño|Habitaciones|Antigüedad|Precio (miles $)|
|---|---|---|---|
|100|2|15|150|
|120|3|10|180|
|150|3|5|220|
|80|1|20|120|
|200|4|2|300|

El modelo será:

$$\text{Precio} = \theta_0 + \theta_1 \cdot \text{Tamaño} + \theta_2 \cdot \text{Habitaciones} + \theta_3 \cdot \text{Antigüedad}$$

### 5.2 **Implementación del Ejemplo en Python**

```python
import numpy as np
import pandas as pd
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error, r2_score

# Datos
data = {
    'Tamaño': [100, 120, 150, 80, 200],
    'Habitaciones': [2, 3, 3, 1, 4],
    'Antigüedad': [15, 10, 5, 20, 2],
    'Precio': [150, 180, 220, 120, 300]
}

df = pd.DataFrame(data)

# Características y target
X = df[['Tamaño', 'Habitaciones', 'Antigüedad']]
y = df['Precio']

# Dividir en entrenamiento y prueba
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Crear y entrenar el modelo
model = LinearRegression()
model.fit(X_train, y_train)

# Coeficientes y término independiente
print(f"Término independiente (θ₀): {model.intercept_:.2f}")
print("Coeficientes:")
for feature, coef in zip(X.columns, model.coef_):
    print(f"  {feature}: {coef:.2f}")

# Predicciones
y_pred = model.predict(X_test)

# Evaluación
print(f"Error cuadrático medio: {mean_squared_error(y_test, y_pred):.2f}")
print(f"Coeficiente de determinación (R²): {r2_score(y_test, y_pred):.2f}")
```

---

## 6. Problemas Comunes y Soluciones

### 6.1 **Overfitting (Sobreajuste)**

Cuando el modelo se ajusta demasiado a los datos de entrenamiento y no generaliza bien.

**Soluciones:**

- Regularización (Ridge, Lasso)
- Más datos de entrenamiento
- Reducir complejidad del modelo

### 6.2 **Underfitting (Subajuste)**

Cuando el modelo es demasiado simple y no captura la estructura de los datos.

**Soluciones:**

- Aumentar complejidad del modelo
- Incluir más características
- Feature engineering

### 6.3 **Alta Multicolinealidad**

Cuando hay alta correlación entre características.

**Soluciones:**

- Análisis de componentes principales (PCA)
- Eliminar características redundantes
- Regularización Ridge

### 6.4 **Escala de Características**

Cuando las características tienen diferentes escalas.

**Soluciones:**

- Normalización (Min-Max)
- Estandarización (Z-score)

---

## 7. Regularización para Regresión Lineal

### 7.1 **Ridge Regression (L2)**

Añade penalización por la suma de cuadrados de coeficientes:

$$J(\theta) = MSE + \lambda \sum_{j=1}^{n} \theta_j^2$$

### 7.2 **Lasso Regression (L1)**

Añade penalización por la suma de valores absolutos de coeficientes:

$$J(\theta) = MSE + \lambda \sum_{j=1}^{n} |\theta_j|$$

### 7.3 **Implementación de Ridge en Python**

```python
from sklearn.linear_model import Ridge

# Crear y entrenar el modelo con regularización Ridge
ridge_model = Ridge(alpha=1.0)  # alpha es el parámetro de regularización λ
ridge_model.fit(X_train, y_train)

# Coeficientes
print("Coeficientes con Ridge:")
for feature, coef in zip(X.columns, ridge_model.coef_):
    print(f"  {feature}: {coef:.2f}")
```

---

## 8. Interpretación de Resultados

### 8.1 **Coeficientes**

Cada coeficiente representa el cambio esperado en la variable objetivo por unidad de cambio en la característica correspondiente, manteniendo constantes las demás características.

### 8.2 **Intercepto**

Representa el valor esperado de la variable objetivo cuando todas las características son cero.

### 8.3 **R² (Coeficiente de Determinación)**

Mide la proporción de la varianza total de la variable objetivo que es explicada por el modelo.

- **R² = 1**: El modelo explica perfectamente la variación.
- **R² = 0**: El modelo no explica nada de la variación.
- **R² < 0**: El modelo es peor que simplemente usar la media.

---

## 9. Evaluación y Validación del Modelo

### 9.1 **Técnicas de Validación**

- Validación cruzada (k-folds)
- Train/Test Split
- Leave-One-Out Cross-Validation
- Validación cronológica (para series temporales)

### 9.2 **Métricas de Evaluación**

- Error Cuadrático Medio (MSE)
- Raíz del Error Cuadrático Medio (RMSE)
- Error Absoluto Medio (MAE)
- Coeficiente de Determinación (R²)

---

## 10. Resumen de Fórmulas

| **Concepto**         | **Fórmula (`1/(2m)`)**                  | **Fórmula (`1/m`)**                     |
| -------------------- | --------------------------------------- | --------------------------------------- |
| **Función de Coste** | $$J = \frac{1}{2m} \sum (ŷ_i - y_i)^2$$ | $$J = \frac{1}{m} \sum (ŷ_i - y_i)^2$$  |
| **Derivada de `θj`** | $$\frac{1}{m} \sum (ŷ_i - y_i) x_{ij}$$ | $$\frac{2}{m} \sum (ŷ_i - y_i) x_{ij}$$ |
| **Ridge (L2)**       | $$J + \lambda \sum \theta_j^2$$         | $$J + \lambda \sum \theta_j^2$$         |
| **Lasso (L1)**       | $$J + \lambda \sum$$                    | $$\theta_j$$                            |

---

## 11. Consideraciones Prácticas

1. **Preparación de datos**:
    
    - Manejo de valores faltantes
    - Detección y tratamiento de outliers
    - Codificación de variables categóricas
2. **Selección de características**:
    
    - Feature importance
    - Forward/Backward selection
    - Técnicas de reducción de dimensionalidad
3. **Hiperparámetros importantes**:
    
    - Tasa de aprendizaje (α)
    - Parámetro de regularización (λ)
    - Número de iteraciones
4. **Herramientas y librerías**:
    
    - Scikit-learn (Python)
    - StatsModels (Python)
    - R (lm, glmnet)
    - TensorFlow y PyTorch para modelos más complejos

---

## 12. Notas Finales

1. **Coherencia en las fórmulas**:
    
    - `1/(2m)` siempre está en la **función de coste**, y las derivadas usan `1/m`.
    - `1/m` en la función de coste implica derivadas con `2/m`.
2. **Tasa de aprendizaje (`α`)**:
    
    - Si usas `1/m`, reduce `α` a la mitad para compensar el factor `2` en las derivadas.
    - Una tasa de aprendizaje demasiado grande puede causar divergencia
    - Una tasa demasiado pequeña puede hacer que la convergencia sea muy lenta
    -
3. **Regresión lineal vs. otros algoritmos**:
    
    - Ventajas: Interpretabilidad, rapidez, base para modelos complejos
    - Desventajas: Asume relación lineal, sensible a outliers

---

## 13. Ejemplo Gráfico: Convergencia del Modelo de Helados

### Modelo Inicial vs. Final

Imaginemos una visualización de nuestro modelo de ventas de helados:

**Modelo Inicial (a=10, b=3)**:

- Predicciones alejadas de los valores reales
- Error total alto (MSE = 227.5)

**Modelo Final (a=-12.2, b=4.7)**:

- Predicciones muy cercanas a los valores reales
- Error total mínimo
- Captura bien la relación temperatura-ventas

### Proceso de Convergencia

Durante el entrenamiento observaríamos:

1. El modelo inicial empieza lejos de los datos
2. Con cada iteración, la línea se acerca a los puntos
3. La pendiente aumenta desde 3 hasta 4.7
4. El intercepto disminuye desde 10 hasta -12.2
5. El error (MSE) disminuye progresivamente
6. Eventualmente el modelo "converge" y se estabiliza

---

## 14. Aplicaciones Prácticas en Machine Learning

### Predicción de Ventas

- Predecir ventas basadas en gastos de publicidad
- Variables: presupuesto en TV, radio, periódicos, etc.
- Objetivo: optimizar distribución de presupuesto

### Predicción de Precios

- Predecir precios de viviendas, acciones, productos
- Variables: características, históricos, tendencias
- Objetivo: decisiones de inversión o fijación de precios

### Análisis de Influencia

- Determinar qué variables impactan más un resultado
- Variables: cualquier factor medible
- Objetivo: toma de decisiones estratégicas
