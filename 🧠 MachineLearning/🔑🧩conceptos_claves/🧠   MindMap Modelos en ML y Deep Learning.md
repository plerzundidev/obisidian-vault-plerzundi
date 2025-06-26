# Modelos en ML y Deep Learning
## Aprendizaje Supervisado
- **Regresión**
  - Lineal  
    - Uso: Relaciones lineales (ej: precio vs. metros²)  
    - Variantes: Ridge, Lasso (regularización)
  - Árboles de Decisión  
    - Split por: Gini/Entropía  
    - Ventaja: Interpretabilidad
  - Random Forest  
    - Ensamble de árboles con bagging  
    - Reduce overfitting
  - Gradient Boosting (XGBoost, LightGBM)  
    - Corrección secuencial de errores  
    - Dominio en competiciones
  - SVM (Máquinas de Vectores Soporte)  
    - Kernel trick para no-linealidad  
    - Ej: Clasificación de texto

- **Clasificación**
  - Regresión Logística  
    - Probabilidades con sigmoide  
    - Ej: Riesgo crediticio
  - K-Nearest Neighbors (KNN)  
    - Basado en distancia  
    - Sensible a escala
  - Naive Bayes  
    - Basado en probabilidad condicional  
    - Ej: Filtrado de spam

- **Evaluación**  
  - Métricas Regresión: MSE, RMSE, R²  
  - Métricas Clasificación:  
    - Matriz de confusión  
    - Precision/Recall, F1-Score, ROC-AUC  
  - Validación: Cross-validation (k-fold)

## Aprendizaje No Supervisado
- **Clustering**
  - K-Means  
    - Agrupa por distancia al centroide  
    - Desafío: Elegir k óptimo (método del codo)
  - DBSCAN  
    - Basado en densidad  
    - Detecta outliers
  - Jerárquico  
    - Dendrogramas  
    - Enfoque aglomerativo/divisivo
  - GMM (Mezcla de Gaussianas)  
    - Probabilístico  
    - Suavizado de clusters

- **Reducción de Dimensionalidad**
  - PCA (Análisis de Componentes Principales)  
    - Maximiza varianza  
    - Ej: Visualización 3D de datasets
  - t-SNE/UMAP  
    - Conserva estructuras locales  
    - Ideal para visualización
  - LDA (Análisis Discriminante Lineal)  
    - Maximiza separación entre clases

- **Reglas de Asociación**
  - Apriori  
    - "Market basket analysis"  
    - Ej: Productos frecuentemente comprados juntos
  - FP-Growth  
    - Más eficiente que Apriori

- **Detección de Anomalías**
  - Isolation Forest  
    - Aísla outliers con árboles
  - Autoencoders  
    - Reconstruyen datos normales  
    - Ej: Fraude en transacciones

## Deep Learning
- **Arquitecturas Clave**
  - Redes Feedforward (MLP)  
    - Capas densamente conectadas  
    - Usos: Tabular data básico
  - CNNs (Redes Convolucionales)  
    - Kernels para extraer features espaciales  
    - Aplicaciones:  
      - Visión computacional  
      - Detección de objetos (YOLO)
  - RNNs/LSTMs  
    - Manejan secuencias (tiempo/texto)  
    - Ej: Predicción de acciones, traducción
  - Transformers  
    - Mecanismos de atención  
    - Revolucionaron NLP (GPT, BERT)  
    - Usos: Chatbots, resumen de texto
  - GANs (Redes Generativas)  
    - Generador vs Discriminador  
    - Aplicaciones:  
      - Creación de imágenes sintéticas  
      - Data augmentation

- **Técnicas Avanzadas**
  - Transfer Learning  
    - Reutilizar modelos pre-entrenados  
    - Ej: Fine-tuning de ResNet para datasets pequeños
  - Regularización  
    - Dropout  
    - Batch Normalization  
    - Data Augmentation
  - Optimización  
    - Algoritmos: Adam, RMSProp  
    - Learning rate scheduling

- **Frameworks**  
  - TensorFlow/Keras  
  - PyTorch (flexibilidad en investigación)  
  - ONNX (interoperabilidad)

## Consideraciones Generales
- **Selección de Modelo**  
  - Tamaño del dataset  
  - Naturaleza del problema (estructurado vs no estructurado)  
  - Recursos computacionales
- **Despliegue**  
  - Optimización: Quantization, pruning  
  - Herramientas: TensorFlow Serving, ONNX Runtime
- **Ética**  
  - Interpretabilidad (SHAP, LIME)  
  - Sesgo en modelos (FairML)
- **Retos Comunes**  
  - Overfitting/Underfitting  
  - Data drift en producción  
  - Coste de entrenamiento (LLMs)