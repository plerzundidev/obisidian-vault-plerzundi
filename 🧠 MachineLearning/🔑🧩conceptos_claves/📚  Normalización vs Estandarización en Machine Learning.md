## Tabla de Contenidos
1. [Introducción](#introducción)  
2. [¿Qué es Normalización?](#qué-es-normalización)  
3. [¿Qué es Estandarización?](#qué-es-estandarización)  
4. [Comparación Detallada](#comparación-detallada)  
5. [¿Cuándo Usar Cada Técnica?](#cuándo-usar-cada-técnica)  
6. [Implementación Práctica](#implementación-práctica)  
7. [Casos de Uso por Dominio](#casos-de-uso-por-dominio)  
8. [Errores Comunes y Buenas Prácticas](#errores-comunes-y-buenas-prácticas)  
9. [Preguntas Frecuentes](#preguntas-frecuentes)  

---

## Introducción
<a name="introducción"></a>
La **normalización** y **estandarización** son técnicas fundamentales para escalar datos en Machine Learning. Su objetivo es garantizar que las variables contribuyan equitativamente al modelo, evitando sesgos por diferencias de escala.

---

## ¿Qué es Normalización? <a name="qué-es-normalización"></a>
### Definición
Transforma los datos para que estén en un **rango específico** (típicamente [0, 1]).  
**Fórmula**:  
$$[
X_{\text{norm}} = \frac{X - X_{\text{min}}}{X_{\text{max}} - X_{\text{min}}}
]$$

### Características Clave
- **Rango**: [0, 1] o personalizado (ej: [-1, 1]).  
- **Efecto en Distribución**: Mantiene la forma original.  
- **Outliers**: Altamente sensible. Un solo outlier extremo distorsiona el escalado.  

### Ejemplo Visual

Datos Originales: [10, 20, 30, 40, 50]  
Normalizados: [0.0, 0.25, 0.5, 0.75, 1.0]


---

## ¿Qué es Estandarización? <a name="qué-es-estandarización"></a>
### Definición
Ajusta los datos para tener **media = 0** y **desviación estándar = 1**.  
**Fórmula**:  
$$[
X_{\text{std}} = \frac{X - \mu}{\sigma}
]$$

### Características Clave
- **Distribución**: Asume forma gaussiana (no obligatoria, pero ideal).  
- **Outliers**: Más robusta que la normalización.  
- **Interpretación**: Valores negativos/positivos indican desviaciones respecto a la media.  

### Ejemplo Visual

Datos Originales: [10, 20, 30, 40, 50]  
Estandarizados: [-1.41, -0.71, 0.0, 0.71, 1.41]


---

## Comparación Detallada <a name="comparación-detallada"></a>
| **Aspecto**               | **Normalización (Min-Max)**       | **Estandarización (Z-Score)**      |
|---------------------------|------------------------------------|-------------------------------------|
| **Rango**                 | [0, 1] o definido                 | Sin límites                         |
| **Media (μ)**             | No controlada                     | 0                                   |
| **Desviación Estándar (σ)**| Variable                          | 1                                   |
| **Outliers**              | Alta sensibilidad                 | Robustez moderada                   |
| **Algoritmos Recomendados**| KNN, Redes Neuronales, K-Means    | SVM, Regresión Lineal, PCA          |

---

## ¿Cuándo Usar Cada Técnica? <a name="cuándo-usar-cada-técnica"></a>
### Normalización ✅
- **Algoritmos basados en distancias**:  
  - K-Nearest Neighbors (KNN): Las distancias euclidianas son sensibles a la escala.  
  - K-Means: Evita que clusters sean dominados por variables de mayor magnitud.  
- **Redes Neuronales**: Acelera la convergencia en optimización (ej.: usar activaciones como Sigmoid).  
- **Procesamiento de imágenes**: Escalar píxeles a [0, 1] o [-1, 1].  

### Estandarización ✅
- **Algoritmos basados en gradientes**:  
  - Regresión Lineal/Logística: Mejora la interpretación de coeficientes.  
  - SVM: Optimiza el margen de separación entre clases.  
- **Técnicas estadísticas**: PCA (maximiza la varianza en componentes principales).  
- **Datos con distribución normal**: Aprovecha propiedades estadísticas.  

---

## Implementación Práctica <a name="implementación-práctica"></a>
### Código en Python (scikit-learn)

```python

from sklearn.preprocessing import MinMaxScaler, StandardScaler

# Normalización
minmax_scaler = MinMaxScaler(feature_range=(0, 1))
X_normalized = minmax_scaler.fit_transform(X_train)

# Estandarización
std_scaler = StandardScaler()
X_standardized = std_scaler.fit_transform(X_train)

# ¡Importante! Aplica misma transformación a datos de prueba:
X_test_normalized = minmax_scaler.transform(X_test)
X_test_standardized = std_scaler.transform(X_test)
```


## Buenas Prácticas en el Escalado de Datos <a name="errores-comunes-y-buenas-prácticas"></a>

### 1. Dividir Datos Antes de Escalar
- **Problema**: Si escalas antes de dividir en entrenamiento y prueba, introduces *data leakage* (filtración de información).  
- **Solución**:  
  1. Divide el dataset en `train` y `test`.  
  2. Ajusta el scaler **solo con los datos de entrenamiento**.  
  3. Aplica la misma transformación a los datos de prueba.  

**Código Ejemplo**:
```python
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler

# Dividir datos
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

# Ajustar scaler SOLO en train
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)

# Aplicar misma transformación a test
X_test_scaled = scaler.transform(X_test)
```

### 2. Manejo de Outliers

- **Normalización (Min-Max)**:
    
    - **Riesgo**: Un solo outlier extremo comprime el rango del resto de los datos.
    - **Solución**:
        
        - Eliminar outliers antes de escalar.
            
        - Usar técnicas como **winsorization** (limitar valores extremos).   
- **Estandarización (Z-Score)**:
    - **Ventaja**: Menos sensible a outliers que Min-Max.
    - **Alternativa**: Usar `RobustScaler` (basado en medianas y rangos intercuartílicos).


**Código Ejemplo con RobustScaler**:	

```python
from sklearn.preprocessing import RobustScaler

robust_scaler = RobustScaler()
X_robust = robust_scaler.fit_transform(X_train)
```


### 3. Guardar Parámetros del Scaler para Producción

- **Problema**: En producción, debes aplicar la misma transformación que usaste en entrenamiento.
- **Solución**: Serializar el scaler (ej.: con `pickle` o `joblib`).

**Código Ejemplo**:

```python
import joblib

# Guardar scaler
joblib.dump(scaler, 'scaler.pkl')

# Cargar scaler en producción
loaded_scaler = joblib.load('scaler.pkl')
X_new_scaled = loaded_scaler.transform(X_new)
```

### 4. Visualizar el Efecto del Escalado

- **Herramientas**:
    - Histogramas y boxplots para comparar distribuciones antes/después.
    - Gráficos de densidad para ver cambios en la forma.

**Código Ejemplo con Matplotlib**:

```python
import matplotlib.pyplot as plt

fig, axes = plt.subplots(1, 2, figsize=(12, 4))

# Antes del escalado
axes[0].hist(X_train[:, 0], bins=30, color='blue', alpha=0.7)
axes[0].set_title('Datos Originales')

# Después del escalado
axes[1].hist(X_train_scaled[:, 0], bins=30, color='red', alpha=0.7)
axes[1].set_title('Datos Estandarizados')

plt.show()
```

### 5. No Escalar Variables Categóricas o Binarias

- **Variables Categóricas**:
    - Ejemplo: Género ("Hombre", "Mujer").
    - **Solución**: Usar `OneHotEncoder` o `OrdinalEncoder`.
- **Variables Binarias (0/1)**:
    - No requieren escalado (ya están en un rango acotado).

**Código Ejemplo**:

```python
from sklearn.preprocessing import OneHotEncoder

encoder = OneHotEncoder()
X_categorical_encoded = encoder.fit_transform(X_categorical)
```


### 6. Usar Pipelines para Automatizar

- **Ventaja**: Evita errores manuales y asegura consistencia.
- **Herramienta**: `sklearn.pipeline.Pipeline`.


**Código Ejemplo**:

```python

from sklearn.pipeline import Pipeline
from sklearn.linear_model import LogisticRegression

# Crear pipeline: escalado + modelo
pipeline = Pipeline([
    ('scaler', StandardScaler()),
    ('model', LogisticRegression())
])

# Entrenar y predecir
pipeline.fit(X_train, y_train)
y_pred = pipeline.predict(X_test)
```

### 7. Validar con Métricas Clave

- **Métricas a Monitorear**:
    - **Precisión**: ¿Mejora el modelo después de escalar?
    - **Tiempo de Convergencia**: ¿El modelo tarda menos en entrenar?
        
- **Técnica**: Usar validación cruzada para comparar resultados.

**Ejemplo con Cross-Validation**:

```python

from sklearn.model_selection import cross_val_score

# Sin escalar
scores_raw = cross_val_score(LogisticRegression(), X_train, y_train, cv=5)

# Con escalado
pipeline = Pipeline([('scaler', StandardScaler()), ('model', LogisticRegression())])
scores_scaled = cross_val_score(pipeline, X_train, y_train, cv=5)

print(f"Sin escalar: {scores_raw.mean():.2f} | Con escalado: {scores_scaled.mean():.2f}")
```

### 8. Considerar el Contexto del Dominio <a name="casos-de-uso-por-dominio"></a>

La elección entre normalización y estandarización debe alinearse con las convenciones y necesidades específicas de cada industria. Aquí más ejemplos:

---

#### Ejemplo 1: **Bioinformática** 🧬
- **Datos**: Expresión génica (ej.: valores de 0 a 10,000 en microarrays).  
- **Problema**: Genes con rangos de expresión muy dispares (ej.: un gen varía entre 0-100 y otro entre 0-10,000).  
- **Técnica Recomendada**: **Normalización (Min-Max)**.  
- **Razón**:  
  - Permite comparar genes en una escala común (ej.: [0, 1]).  
  - Evita que genes altamente expresivos dominen análisis de clustering.  

---

#### Ejemplo 2: **Energía Renovable** ☀️
- **Datos**: Radiación solar (0–1000 W/m²) y velocidad del viento (0–20 m/s).  
- **Problema**: Modelar la relación entre variables para predecir producción energética.  
- **Técnica Recomendada**: **Estandarización**.  
- **Razón**:  
  - Si se usa una regresión lineal, los coeficientes reflejarán mejor el impacto relativo de cada variable.  
  - La radiación solar tiene mayor magnitud, pero no necesariamente mayor importancia.  

---

#### Ejemplo 3: **Marketing Digital** 📱
- **Datos**:  
  - Tiempo en página (segundos).  
  - Número de clics (entero no negativo).  
- **Problema**: Segmentar usuarios con K-Means para campañas personalizadas.  
- **Técnica Recomendada**: **Normalización**.  
- **Razón**:  
  - Sin escalado, "tiempo en página" (ej.: 0-600 segundos) dominaría sobre "clics" (0-10).  
  - Equilibra la contribución de ambas variables en el cálculo de distancias.  

---

#### Ejemplo 4: **Agricultura de Precisión** 🌾
- **Datos**:  
  - Humedad del suelo (0–100%).  
  - Concentración de nitrógeno (0–500 ppm).  
- **Problema**: Predecir rendimiento de cultivos con una red neuronal.  
- **Técnica Recomendada**: **Normalización (a [-1, 1])**.  
- **Razón**:  
  - Las activaciones como *tanh* funcionan mejor con entradas en rangos simétricos.  
  - Acelera la convergencia del gradiente descendente.  

---

#### Ejemplo 5: **Finanzas Cuantitativas** 💹
- **Datos**:  
  - Retornos diarios de acciones (-5% a +5%).  
  - Volumen de transacciones (0–10 millones de USD).  
- **Problema**: Detección de anomalías con Autoencoders.  
- **Técnica Recomendada**: **Estandarización**.  
- **Razón**:  
  - El volumen tiene una distribución con colas pesadas (outliers comunes).  
  - La estandarización hace que los errores de reconstrucción sean comparables entre variables.  

---

#### Ejemplo 6: **Audio Processing** 🎵
- **Datos**: Amplitud de onda (valores entre -1 y 1).  
- **Problema**: Entrenar un modelo para generar música con GANs.  
- **Técnica Recomendada**: **No escalar** (o normalizar a [-1, 1]).  
- **Razón**:  
  - Los datos ya están en un rango acotado por naturaleza.  
  - Las capas de normalización dentro de la GAN (ej.: BatchNorm) manejan el escalado interno.  

---

#### Ejemplo 7: **Logística** 🚚
- **Datos**:  
  - Tiempo de entrega (horas).  
  - Distancia recorrida (kilómetros).  
- **Problema**: Optimizar rutas con un modelo de regresión.  
- **Técnica Recomendada**: **Estandarización**.  
- **Razón**:  
  - Si la distancia tiene una distribución sesgada (ej.: mayoría de pedidos locales, pocos internacionales), la estandarización reduce el sesgo en los coeficientes.  

---

#### Ejemplo 8: **Videojuegos** 🎮
- **Datos**:  
  - Puntuación del jugador (0–1000 puntos).  
  - Tiempo de juego diario (0–8 horas).  
- **Problema**: Sistema de recomendación de desafíos con SVM.  
- **Técnica Recomendada**: **Estandarización**.  
- **Razón**:  
  - SVM utiliza márgenes de separación basados en distancias.  
  - La puntuación podría tener outliers (ej.: jugadores profesionales con 5000 puntos).  

---

#### Ejemplo 9: **Salud Mental** 🧠
- **Datos**:  
  - Escala de ansiedad (1–10, ordinal).  
  - Número de sesiones terapéuticas (0–20).  
- **Problema**: Clasificar riesgo de depresión con Regresión Logística.  
- **Técnica Recomendada**: **Estandarización**.  
- **Razón**:  
  - La regresión logística usa descenso de gradiente, que converge más rápido con datos estandarizados.  
  - La escala ordinal (1–10) no es lineal en su impacto (mejor tratarla como numérica continua).  

---

#### Ejemplo 10: **Space Exploration** 🚀
- **Datos**:  
  - Temperatura exterior (-150°C a +150°C).  
  - Presión atmosférica (0–1000 hPa).  
- **Problema**: Predecir fallos en sensores con Random Forest.  
- **Técnica Recomendada**: **No escalar**.  
- **Razón**:  
  - Los modelos basados en árboles (como Random Forest) no requieren escalado.  
  - Ahorra tiempo de preprocesamiento sin afectar el rendimiento.  