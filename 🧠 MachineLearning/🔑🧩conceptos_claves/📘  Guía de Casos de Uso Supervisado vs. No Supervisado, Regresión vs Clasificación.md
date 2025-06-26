
## Tabla de Contenidos
1. [Modelos Supervisados](#modelos-supervisados)  
   - [Regresión](#regresión)  
   - [Clasificación](#clasificación)  
2. [Modelos No Supervisados](#modelos-no-supervisados)  
   - [Clustering](#clustering)  
   - [Asociación](#asociación)  
   - [Reducción de Dimensionalidad](#reducción-de-dimensionalidad)  
3. [Cuándo Usar Cada Enfoque](#cuándo-usar-cada-enfoque)  
4. [Comparación Rápida](#comparación-rápida)  
5. [Flujo de Decisión](#flujo-de-decisión)  

---

## Modelos Supervisados <a name="modelos-supervisados"></a>
Los modelos supervisados aprenden de datos **etiquetados** (input → output conocido). Se dividen en:

---

### Regresión <a name="regresión"></a>
**Objetivo**: Predecir un valor **numérico continuo**.  

#### Casos de Uso por Dominio
| **Dominio**       | **Problema**                          | **Algoritmos Comunes**       | **Ejemplo Detallado**                          |
|--------------------|---------------------------------------|-----------------------------|------------------------------------------------|
| **Bienes Raíces**  | Predecir precios de viviendas         | Regresión Lineal, Random Forest | Precio basado en metros cuadrados, ubicación, etc. |
| **Energía**        | Pronosticar demanda eléctrica         | SARIMA, LSTM                 | Predecir consumo por hora para optimizar generación. |
| **Finanzas**       | Estimar ROI de inversiones            | Gradient Boosting, XGBoost   | Calcular retorno esperado en base a riesgo y mercado. |
| **Salud**          | Predecir niveles de glucosa           | Redes Neuronales             | Monitoreo continuo para pacientes diabéticos. |
| **Agricultura**    | Estimación de rendimiento de cultivos | Regresión Polinomial         | Relacionar lluvia y fertilizantes con producción. |

---

### Clasificación <a name="clasificación"></a>

**Objetivo**: Asignar una **etiqueta categórica**.  

#### Casos de Uso por Dominio
| **Dominio**       | **Problema**                          | **Algoritmos Comunes**       | **Ejemplo Detallado**                          |
|--------------------|---------------------------------------|-----------------------------|------------------------------------------------|
| **Marketing**      | Detectar clientes potenciales         | Logistic Regression, SVM     | Clasificar leads como "calificados" o "no calificados". |
| **Salud**          | Diagnóstico de enfermedades           | Random Forest, Redes Neuronales | Identificar cáncer en mamografías (clase binaria: maligno/benigno). |
| **Seguridad**      | Detección de fraude                   | XGBoost, Isolation Forest    | Clasificar transacciones como "legítimas" o "fraudulentas". |
| **Recursos Humanos**| Selección de candidatos               | Naive Bayes, Decision Trees  | Filtrar CVs como "aptos" o "no aptos" para un puesto. |
| **Ecología**       | Identificación de especies            | CNN (Computer Vision)        | Clasificar imágenes de animales en selvas. |

---

## Modelos No Supervisados <a name="modelos-no-supervisados"></a>
Los modelos no supervisados trabajan con datos **sin etiquetar** para encontrar patrones intrínsecos. Se dividen en:

---

### Clustering <a name="clustering"></a>
**Objetivo**: Agrupar datos similares.  

#### Casos de Uso por Dominio
| **Dominio**       | **Problema**                          | **Algoritmos Comunes**       | **Ejemplo Detallado**                          |
|--------------------|---------------------------------------|-----------------------------|------------------------------------------------|
| **Retail**         | Segmentación de clientes              | K-Means, DBSCAN             | Agrupar clientes por hábitos de compra. |
| **Genómica**       | Clasificación de tipos celulares      | Hierarchical Clustering     | Identificar subtipos de células cancerígenas. |
| **Social Media**   | Detección de comunidades              | Spectral Clustering         | Encontrar grupos de usuarios con intereses similares en redes. |
| **Astronomía**     | Agrupación de galaxias                | Mean-Shift                  | Clasificar galaxias por forma y brillo. |
| **Transporte**     | Optimización de rutas                 | Clustering Geográfico       | Agrupar pedidos por ubicación para repartos eficientes. |

---

### Asociación <a name="asociación"></a>
**Objetivo**: Descubrir reglas de asociación entre variables.  

#### Casos de Uso por Dominio
| **Dominio**       | **Problema**                          | **Algoritmos Comunes**       | **Ejemplo Detallado**                          |
|--------------------|---------------------------------------|-----------------------------|------------------------------------------------|
| **Retail**         | Market Basket Analysis                | Apriori, FP-Growth          | Descubrir que los clientes que compran pan suelen comprar mantequilla. |
| **Medicina**       | Interacción entre síntomas            | Apriori                     | Relacionar síntomas comunes en enfermedades crónicas. |
| **Streaming**      | Recomendación de contenido            | Reglas de Asociación         | Si un usuario ve "Stranger Things", sugerir "Dark". |

---

### Reducción de Dimensionalidad <a name="reducción-de-dimensionalidad"></a>
**Objetivo**: Simplificar datos preservando su esencia.  

#### Casos de Uso por Dominio
| **Dominio**       | **Problema**                          | **Algoritmos Comunes**       | **Ejemplo Detallado**                          |
|--------------------|---------------------------------------|-----------------------------|------------------------------------------------|
| **Imágenes**       | Compresión de características         | PCA, t-SNE                  | Reducir 1000 píxeles a 30 componentes principales. |
| **Finanzas**       | Análisis de riesgo simplificado       | PCA                         | Combinar variables económicas en índices. |
| **Bioinformática** | Análisis de expresión génica          | UMAP                        | Visualizar patrones en datos de RNA-seq. |

---

## Cuándo Usar Cada Enfoque <a name="cuándo-usar-cada-enfoque"></a>

### Modelos Supervisados ✅
- **Cuando tienes datos etiquetados** y un objetivo claro (ej.: predecir, clasificar).  
- **Ejemplos**:  
  - Predecir ventas (Regresión).  
  - Diagnosticar enfermedades (Clasificación).  

### Modelos No Supervisados ✅
- **Cuando no hay etiquetas** y buscas explorar datos o encontrar patrones ocultos.  
- **Ejemplos**:  
  - Segmentar clientes (Clustering).  
  - Reducir ruido en imágenes (Reducción de dimensionalidad).  

---

## Comparación Rápida <a name="comparación-rápida"></a>
| **Aspecto**         | **Supervisado**                      | **No Supervisado**                  |
|----------------------|--------------------------------------|--------------------------------------|
| **Datos**            | Etiquetados (X → y)                 | Sin etiquetas                        |
| **Objetivo**         | Predecir/Clasificar                 | Descubrir patrones                   |
| **Ejemplos**         | Regresión, Clasificación            | Clustering, Asociación               |
| **Complejidad**      | Mayor (requiere etiquetas precisas) | Menor (exploratorio)                 |
| **Aplicación Típica**| Problemas de negocio definidos       | Análisis exploratorio (EDA)          |

---

## Flujo de Decisión <a name="flujo-de-decisión"></a>

1. **¿Tienes datos etiquetados?**  
   - **Sí** → ¿Qué tipo de variable es el target?  
     - **Numérica** → **Regresión**.  
     - **Categórica** → **Clasificación**.  
   - **No** → ¿Qué necesitas lograr?  
     - **Agrupar datos** → **Clustering**.  
     - **Descubrir reglas** → **Asociación**.  
     - **Simplificar datos** → **Reducción de dimensionalidad**.  

---

## Preguntas Frecuentes
### 1. ¿Puedo combinar enfoques supervisados y no supervisados?
- **Sí**. Ejemplo: Usar PCA (no supervisado) para reducir dimensiones antes de entrenar una SVM (supervisado).  

### 2. ¿Qué hacer si no tengo etiquetas pero necesito predecir algo?
- **Opción 1**: Etiquetar manualmente un subconjunto y usar aprendizaje semi-supervisado.  
- **Opción 2**: Usar clustering para crear etiquetas proxy y luego entrenar un modelo supervisado.  

### 3. ¿La regresión es siempre supervisada?
- **Sí**, porque requiere un target numérico conocido para aprender.  

---

## Conclusión
- **Supervisado**: Ideal para problemas con objetivos claros y datos etiquetados.  
- **No Supervisado**: Perfecto para explorar datos, encontrar patrones o preprocesar.  
- **Regresión vs. Clasificación**: Depende de si el target es numérico o categórico.  