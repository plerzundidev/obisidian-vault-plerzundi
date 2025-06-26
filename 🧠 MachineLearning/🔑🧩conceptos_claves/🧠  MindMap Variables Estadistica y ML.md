# Variables en Estadística y ML
## Estadística
- **Tipos de Variables**
  - Cualitativas (no numéricas)
    - *Nominales*: Sin orden intrínseco  
      Ej: Género (M/F), Tipo de fruta, País  
      Técnicas: Codificación one-hot, frecuencia
    - *Ordinales*: Jerarquía definida  
      Ej: Nivel educativo (Primaria < Secundaria < Universitario)  
      Técnicas: Label encoding ordinal
  - Cuantitativas (numéricas)
    - *Discretas*: Valores enteros contables  
      Ej: Número de hijos (0,1,2), Reviews de producto (1-5 estrellas)
    - *Continuas*: Medidas con decimales  
      Ej: Temperatura (36.5°C), Ingresos anuales ($43,250.75)
- **Relación entre Variables**
  - Dependiente (Y): Variable a predecir/explicar  
    Ej: Precio de casa, Diagnóstico médico
  - Independiente (X): Variables predictoras  
    Ej: Metros cuadrados, Síntomas clínicos
- **Escalas de Medición**
  - *Nominal*: Solo categorías (ej: colores)
  - *Ordinal*: Orden sin distancia definida (ej: nivel dolor 1-10)
  - *Intervalo*: Diferencias significativas sin cero absoluto (ej: temperatura en °C)
  - *Razón*: Cero absoluto y operaciones matemáticas válidas (ej: peso, ingresos)

## Machine Learning
- **Variables de Entrada (Features)**
  - Numéricas  
    - Enteros (int): Edad, Conteo de productos  
    - Flotantes (float): Ratio de conversión, Porcentajes
  - Categóricas  
    - *Nominales*: Tipo de tarjeta (Visa/Mastercard) → One-hot encoding  
    - *Ordinales*: Tamaño (S/M/L/XL) → Mapeo ordinal
  - Fecha/Hora  
    - Extracción de features: Día de semana, Mes, Hora pico  
    - Transformaciones: Timestamp UNIX, Duración
  - Texto  
    - Técnicas: Bag-of-words, TF-IDF, Embeddings (Word2Vec)
  - Imágenes  
    - Representación: Pixeles (arrays numéricos)  
    - Transformaciones: Normalización [0-1], Augmentation
- **Variable Objetivo (Target)**
  - Regresión: Valor continuo  
    Ej: Precio de acciones, Consumo energético
  - Clasificación  
    - Binaria (2 clases): Spam/No spam  
    - Multiclase (>2 clases exclusivas): Tipo de flor (Iris setosa/virginica)  
    - Multietiqueta (múltiples clases no excluyentes): Etiquetas de película (Acción, Drama)
- **Variables Especiales**
  - Latentes: Inferidas en modelos no supervisados (ej: clusters en K-means)
  - Dummy: Para variables categóricas (ej: [1,0] para "Sí/No")
  - Interacciones: Combinación de features (ej: Edad × Ingreso)
  - Polinómicas: Potencias de variables (X², X³) para relaciones no lineales

## Manipulación de Variables
- **Ingeniería de Features**
  - Normalización: Escalar a rango [0-1] (MinMaxScaler)
  - Estandarización: Media=0, Desviación=1 (Z-score)
  - Discretización: Convertir continuas a categóricas (ej: edad → rangos)
  - Imputación de missing values:  
    - Métodos: Media/mediana, KNN, Modelos predictivos
  - Creación de nuevas variables:  
    - Ratio: Ingreso per cápita  
    - Agregación: Promedio histórico por cliente
- **Selección de Variables**
  - Métodos Filter:  
    - Correlación (Pearson, Spearman)  
    - ANOVA (para clasificación)
  - Métodos Wrapper:  
    - Forward/Backward selection  
    - RFE (Recursive Feature Elimination)
  - Métodos Embedded:  
    - Regularización L1 (Lasso)  
    - Importancia en árboles (Feature Importance)
  - Reducción dimensionalidad:  
    - PCA (Componentes principales)  
    - UMAP/t-SNE (para visualización)

## Consideraciones Especiales
- **Leakage de Datos**
  - Temporal: Usar datos futuros para predecir pasado  
    Solución: Time-based split
  - Target Leakage: Variables que contienen información del resultado  
    Ej: Incluir "diagnóstico" para predecir enfermedad
- **Sesgos**
  - Variables protegidas: Género, etnia, religión  
    Técnicas: Eliminación o fairness-aware ML
  - Variables proxy: ZIP code (puede correlacionar con raza)
  - Dataset imbalance: Oversampling (SMOTE), Undersampling
- **Alta Dimensionalidad**
  - Curse of dimensionality:  
    - Sobreajuste en espacios de alta dimensión  
    - Distancias pierden significado
  - Soluciones:  
    - Feature selection estricto  
    - Autoencoders (deep learning)  
    - Manifold learning
- **Variables Temporales**
  - Tratamiento especial para series de tiempo:  
    - Lag features (valores anteriores)  
    - Rolling windows (promedio móvil)  
    - Estacionalidad (diferenciación)