

## 📌 **Introducción**
Imagina que quieres predecir el **costo de un viaje en taxi** ($y$) basado en la **distancia recorrida** ($x$). La regresión lineal simple te permite encontrar una fórmula que relacione estas dos variables. 

**Objetivo:**  
Aprender la tarifa inicial ($\theta_0$) y el costo por kilómetro ($\theta_1$) para hacer predicciones precisas.  

**Ecuación del modelo:**  
$$ \text{Costo} = \theta_0 + \theta_1 \cdot \text{Distancia} $$  

---

## 📜 **Símbolos Clave**

| **Símbolo** | **Significado**        | **Ejemplo en Taxi**                         |
| ----------- | ---------------------- | ------------------------------------------- |
| $x$         | Variable independiente | Distancia recorrida (ej: 5 km, 10 km).      |
| $y$         | Variable dependiente   | Costo real del viaje (ej: \$10.50).         |
| $\theta_0$  | Intercepto             | Tarifa inicial (ej: \$3.50 por subir).      |
| $\theta_1$  | Pendiente              | Costo por kilómetro (ej: \$1.20/km).        |
| $J$         | Función de costo       | Error promedio entre predicciones y reales. |
| $\alpha$    | Tasa de aprendizaje    | Velocidad de ajuste (ej: $0.01$).           |

---

## 🧮 **Función de Costo: Medir el Error**
El error se calcula como el promedio de las diferencias al cuadrado entre las predicciones y los valores reales:  
$$ J(\theta_0, \theta_1) = \frac{1}{2m} \sum_{i=1}^m \left( h_\theta(x^{(i)}) - y^{(i)} \right)^2 $$  

**Ejemplo con datos iniciales:**  
Si $\theta_0 = 0$ y $\theta_1 = 0$ (predicciones en \$0):  

| Distancia (km) | Costo Real (\$) | Predicción | Error Cuadrático |
|----------------|-----------------|------------|------------------|
| 5              | 10.50           | 0          | $(0 - 10.50)^2 = 110.25$ |
| 10             | 16.00           | 0          | $(0 - 16.00)^2 = 256.00$ |
| 15             | 21.50           | 0          | $(0 - 21.50)^2 = 462.25$ |

**Cálculo de $J$:**  
$$ J = \frac{1}{6} (110.25 + 256 + 462.25) \approx 138.08 $$  
*El error promedio es de \$138.08. ¡Debemos reducirlo!*

---

## 🔄 **Gradiente Descendente: Aprender de los Errores**
El algoritmo ajusta $\theta_0$ y $\theta_1$ en pasos pequeños usando derivadas parciales.

### **Paso 1: Calcular Derivadas Parciales**
- **Derivada para $\theta_0$:**  
  $$ \frac{\partial J}{\partial \theta_0} = \frac{1}{3} \left[ (0 - 10.50) + (0 - 16.00) + (0 - 21.50) \right] = -16 $$  

- **Derivada para $\theta_1$:**  
  $$ \frac{\partial J}{\partial \theta_1} = \frac{1}{3} \left[ (0 - 10.50) \cdot 5 + (0 - 16.00) \cdot 10 + (0 - 21.50) \cdot 15 \right] \approx -160.83 $$  

### **Paso 2: Actualizar Parámetros ($\alpha = 0.01$)**  
- **Nuevo $\theta_0$:**  
  $$ \theta_0 := 0 - 0.01 \cdot (-16) = 0.16 $$  
- **Nuevo $\theta_1$:**  
  $$ \theta_1 := 0 - 0.01 \cdot (-160.83) \approx 1.61 $$  

**Nuevas predicciones:**  

- **5 km:**  
  $$ 0.16 + 1.61 \cdot 5 = 8.21\ \$ \quad \text{(Error: } 10.50 - 8.21 = 2.29\ \$ ) $$  

- **10 km:**  
  $$ 0.16 + 1.61 \cdot 10 = 16.26\ \$ \quad \text{(Error: } 16.00 - 16.26 = -0.26\ \$ ) $$  

---

## 📈 **Resultados Después de Varias Iteraciones**

Tras 100 iteraciones, el modelo converge a:  
- $\theta_0 \approx 3.50$ (tarifa inicial aprendida).  
- $\theta_1 \approx 1.10$ (costo por km aprendido).  

**Predicciones finales:**  

| Distancia (km) | Costo Real (\$) | Predicción (\$) | Error (\$) |
|----------------|-----------------|-----------------|------------|
| 5              | 10.50           | $3.50 + 1.10 \cdot 5 = 9.00$ | 1.50       |
| 10             | 16.00           | $3.50 + 1.10 \cdot 10 = 14.50$ | 1.50       |
| 15             | 21.50           | $3.50 + 1.10 \cdot 15 = 20.00$ | 1.50       |

---

## 🧩 **¿Por Qué Son Necesarios Todos los Pasos?**

### **1. Función de Hipótesis**  
- Sin una ecuación, no hay base para predecir.  
- **Ejemplo:** Sin $\theta_0$ y $\theta_1$, no sabrías que el taxi cobra por km.  

### **2. Función de Costo**  
- Mide qué tan mal están las predicciones.  
- **Ejemplo:** Un error de \$138 indica que el modelo inicial es muy malo.  

### **3. Gradiente Descendente**  
- Ajusta los parámetros paso a paso, como subir/bajar el volumen hasta encontrar el nivel ideal.  

### **4. Tasa de Aprendizaje ($\alpha$)**  

- **$\alpha$ grande:** Riesgo de sobrepasar la solución (ej: subir el volumen demasiado).  
- **$\alpha$ pequeño:** Aprendizaje lento pero preciso (ej: ajustar el volumen milímetro a milímetro).  

---

## 💡 **Consejos Clave**  

1. **Elija $\alpha$ cuidadosamente:**  
   - Pruebe valores como $0.001$, $0.01$, o $0.1$.  
2. **Normalice los datos:**  
   - Si la distancia está en km y el costo en miles, escale los datos para acelerar el entrenamiento.  
3. **Verifique la convergencia:**  
   - Si el error no disminuye después de varias iteraciones, reduzca $\alpha$.  

---

## 🎯 **Conclusión para "Gente Común"**  

La regresión lineal simple funciona como un **aprendizaje por ensayo y error**:  
1. Empieza con una suposición inicial (ej: \$0 por todo).  
2. Mide cuán equivocada está (error de \$138).  
3. Ajusta sus suposiciones en dirección contraria al error.  
4. Repite hasta que el error sea mínimo.  

**En la vida real:**  
- Así es como las apps predicen precios, tiempos de entrega, o demandas de productos.  
- ¡Es la base de muchas tecnologías que usas a diario! 🚗💨  



#machinelearning #linealregression #lm 