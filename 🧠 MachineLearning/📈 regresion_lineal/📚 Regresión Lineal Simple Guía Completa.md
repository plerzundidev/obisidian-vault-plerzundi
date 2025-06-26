

La **regresión lineal simple** es un algoritmo de machine learning que modela la relación lineal entre una variable independiente ($x$) y una variable dependiente ($y$). Su objetivo es encontrar una recta que minimice el error entre las predicciones y los valores reales.

**Ecuación del modelo:**  
$$ h_\theta(x) = \theta_0 + \theta_1 x $$  
- $h_\theta(x)$: Predicción de $y$.  
- $\theta_0$: Intercepto (valor de $y$ cuando $x=0$).  
- $\theta_1$: Pendiente (cambio en $y$ por unidad de $x$).  

---

## 📜 **Símbolos Clave**

| **Símbolo** | **Significado** | **Ejemplo** |
|-------------|-----------------|-------------|
| $x^{(i)}$   | Variable independiente | Tamaño de una casa (m²) |
| $y^{(i)}$   | Variable dependiente | Precio de la casa (\$) |
| $\theta_0$  | Intercepto | Costo base de una casa ($x=0$) |
| $\theta_1$  | Pendiente | Aumento en precio por m² |
| $J(\theta_0, \theta_1)$ | Función de costo | Error cuadrático medio |
| $\alpha$    | Tasa de aprendizaje | Velocidad de ajuste (ej: $0.01$) |
| $\frac{\partial}{\partial \theta_j} J$ | Derivada parcial | Dirección para reducir el error |

---

## 🧮 **Función de Costo (Error)**

Mide el error promedio entre las predicciones y los valores reales:  
$$ J(\theta_0, \theta_1) = \frac{1}{2m} \sum_{i=1}^m \left( h_\theta(x^{(i)}) - y^{(i)} \right)^2 $$  
- $m$: Número de ejemplos de entrenamiento.  
- **Ejemplo:** Si $\theta_0 = 0$, $\theta_1 = 1$, y los datos son $(1,1), (2,2), (3,3)$:  
  $$ J = \frac{1}{6} \left[ (1-1)^2 + (2-2)^2 + (3-3)^2 \right] = 0 $$

---

## 🔄 **Gradiente Descendente**
Algoritmo para minimizar $J(\theta_0, \theta_1)$. Actualiza $\theta_0$ y $\theta_1$ iterativamente.

### **Fórmulas de Actualización**  
$$ \theta_j := \theta_j - \alpha \cdot \frac{\partial}{\partial \theta_j} J $$  

### **Derivadas Parciales**  
- Para $\theta_0$:  
  $$ \frac{\partial}{\partial \theta_0} J = \frac{1}{m} \sum_{i=1}^m \left( h_\theta(x^{(i)}) - y^{(i)} \right) $$  
- Para $\theta_1$:  
  $$ \frac{\partial}{\partial \theta_1} J = \frac{1}{m} \sum_{i=1}^m \left( h_\theta(x^{(i)}) - y^{(i)} \right) x^{(i)} $$  

---

## 🛠️ **Ejemplo Práctico: Predicción de Precios de Casas**

### **Datos de Entrenamiento**
| Tamaño ($x$) | Precio ($y$) |
|--------------|--------------|
| 40 m²        | 100\$         |
| 60 m²        | 150\$         |
| 80 m²        | 200\$         |

### **Paso 1: Inicializar Parámetros**  
$$ \theta_0 = 0, \quad \theta_1 = 0 $$

### **Paso 2: Calcular Predicciones Iniciales**  
$$ h_\theta(40) = 0, \quad h_\theta(60) = 0, \quad h_\theta(80) = 0 $$

### **Paso 3: Calcular Función de Costo Inicial**  
$$ J = \frac{1}{6} \left[ (-100)^2 + (-150)^2 + (-200)^2 \right] \approx 12,083.33 $$

### **Paso 4: Calcular Derivadas Parciales**  

- $\frac{\partial}{\partial \theta_0} J = \frac{-450}{3} = -150$  
- $\frac{\partial}{\partial \theta_1} J = \frac{-29,000}{3} \approx -9,666.67$  

---

### **Paso 5: Actualizar Parámetros ($\alpha = 0.0001$)**  

**Aplicación detallada de $\alpha$:**  
1. **Para $\theta_0$:**  
   $$ \theta_0 := \theta_0 - \alpha \cdot \frac{\partial}{\partial \theta_0} J $$  
   $$ \theta_0 := 0 - 0.0001 \cdot (-150) = 0 + 0.015 = 0.015 $$  

2. **Para $\theta_1$:**  
   $$ \theta_1 := \theta_1 - \alpha \cdot \frac{\partial}{\partial \theta_1} J $$  
   $$ \theta_1 := 0 - 0.0001 \cdot (-9,666.67) \approx 0 + 0.9667 = 0.9667 $$  

**¿Por qué se suma?**  
- Las derivadas son negativas (error alto), y al multiplicar por $\alpha$ y restar, se convierte en una suma:  
  $$ \theta_j := \theta_j - (\text{valor negativo}) = \theta_j + (\text{valor positivo}) $$  

---

### **Paso 6: Nueva Predicción**  
$$ h_\theta(40) = 0.015 + 0.9667 \times 40 \approx 38.68\$ $$  
**Nuevo error:** $$ J \approx 10,200.5 $$ *(¡Error reducido en un 15%!)*  

---

## ❓ **¿Por qué Inicializar en $\theta_0 = 0$ y $\theta_1 = 0$?**  

1. **Simplicidad:** Facilita cálculos manuales y explicaciones pedagógicas.  
2. **Neutralidad:** Evita sesgos iniciales en el modelo.  
3. **Claridad Visual:** Muestra cómo el gradiente descendente corrige el error desde cero.  

---

## 📉 **Convergencia del Modelo**

- **Graficar $J$ vs. Iteraciones:** Si la curva se estabiliza, el modelo ha convergido.  
- **Tolerancia:** Detener el algoritmo si $\Delta J < 0.001$.  

---

## 💡 **Consejos Clave**  

1. **Normalizar datos:** Escala $x$ e $y$ si están en rangos muy diferentes.  
2. **Ajustar $\alpha$:**  
   - **$\alpha$ grande (ej: $0.1$):** Riesgo de divergencia.  
   - **$\alpha$ pequeño (ej: $0.000001$):** Entrenamiento lento.  
   - **Valor óptimo:** Prueba $0.001$, $0.01$, $0.1$.  
3. **Verificar derivadas:** Usa ejemplos pequeños para validar cálculos.  


#machinelearning #linealregression #lm 
