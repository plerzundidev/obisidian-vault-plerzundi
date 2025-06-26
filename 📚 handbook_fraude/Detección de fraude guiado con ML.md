

# 1. Introducción  

La mejor manera de comprender los desafíos subyacentes en el diseño de un sistema de detección de fraudes en tarjetas de crédito (FDS, por sus siglas en inglés) es diseñando uno. Este capítulo presenta una implementación de un FDS de referencia y cubre los principales pasos que deben considerarse.  

La **Sección 3.2** describe primero un simulador simple para datos de transacciones de tarjetas de pago. Aunque es simplista, el simulador proporciona un entorno lo suficientemente desafiante para abordar un problema típico de detección de fraudes. En particular, el simulador permitirá generar conjuntos de datos que: i) están altamente desbalanceados (baja proporción de transacciones fraudulentas), ii) contienen variables tanto numéricas como categóricas (con características categóricas que tienen un número muy alto de valores posibles), y iii) presentan escenarios de fraude dependientes del tiempo.  

Las **Secciones 3.3 y 3.4** abordan los dos pasos principales de un proceso estándar de modelado predictivo: transformación de características y modelado predictivo. Estas secciones proporcionarán algunas estrategias de referencia para realizar transformaciones de características significativas y construir un primer modelo predictivo cuya precisión servirá como referencia en el resto del libro.  

Finalmente, en la **Sección 3.5**, aplicamos esta metodología de referencia (transformación de características y modelado predictivo) a un conjunto de datos del mundo real de transacciones con tarjetas y demostramos su capacidad para detectar efectivamente transacciones fraudulentas.  



# 2. Simulador de datos de transacciones  

Esta sección presenta un simulador de datos de transacciones de transacciones legítimas y fraudulentas. Este simulador se utilizará a lo largo del resto de este libro para motivar y evaluar la eficiencia de diferentes técnicas de detección de fraudes de manera reproducible.  

Una simulación es necesariamente una aproximación a la realidad. En comparación con la complejidad de las dinámicas subyacentes en los datos de transacciones de tarjetas de pago del mundo real, el simulador de datos que presentamos a continuación sigue un diseño simple.  

Este diseño simple es una elección deliberada. En primer lugar, tener reglas simples para generar transacciones y comportamientos fraudulentos ayudará a interpretar el tipo de patrones que las diferentes técnicas de detección de fraudes pueden identificar. En segundo lugar, aunque simple en su diseño, el simulador de datos generará conjuntos de datos que son desafiantes de manejar.  

Los conjuntos de datos simulados resaltarán la mayoría de los problemas que los profesionales de la detección de fraudes enfrentan al utilizar datos del mundo real. En particular, incluirán desbalanceo de clases (menos del 1% de transacciones fraudulentas), una mezcla de características numéricas y categóricas (con características categóricas que involucran un número muy grande de valores), relaciones no triviales entre características y escenarios de fraude dependientes del tiempo.  



### Decisiones de diseño  
#### Características de las transacciones  
Nuestro enfoque estará en las características más esenciales de una transacción. En esencia, una transacción con tarjeta de pago consiste en cualquier cantidad pagada a un comerciante por un cliente en un momento determinado. Por lo tanto, las seis características principales que resumen una transacción son:  

1. **ID de la transacción (TRANSACTION_ID)**: Un identificador único para la transacción.  
2. **Fecha y hora (TX_DATETIME)**: Fecha y hora en la que ocurre la transacción.  
3. **ID del cliente (CUSTOMER_ID)**: El identificador del cliente. Cada cliente tiene un identificador único.  
4. **ID del terminal (TERMINAL_ID)**: El identificador del comerciante (o más precisamente, del terminal). Cada terminal tiene un identificador único.  
5. **Monto de la transacción (TX_AMOUNT)**: La cantidad de la transacción.  
6. **Etiqueta de fraude (TX_FRAUD)**: Una variable binaria, con el valor `0` para una transacción legítima o el valor `1` para una transacción fraudulenta.  

Estas características se denominarán **TRANSACTION_ID**, **TX_DATETIME**, **CUSTOMER_ID**, **TERMINAL_ID**, **TX_AMOUNT** y **TX_FRAUD**.  

El objetivo del simulador de datos de transacciones será generar una tabla de transacciones con estas características. Esta tabla se denominará **tabla de transacciones etiquetadas**. En la **Figura 1** se ilustra un ejemplo de dicha tabla.  

![[Pasted image 20250307021728.png]]
**Fig. 1.** Ejemplo de una tabla de transacciones etiquetadas. Cada transacción se representa como una fila en la tabla, junto con su etiqueta (variable **TX_FRAUD**, `0` para transacciones legítimas y `1` para transacciones fraudulentas).  


```python
# Necessary imports for this notebook
import os

import numpy as np
import pandas as pd

import datetime
import time

import random

# For plotting
%matplotlib inline

import matplotlib.pyplot as plt
import seaborn as sns

sns.set_style('darkgrid', {'axes.facecolor': '0.9'})
```



### 2.1. Generación de perfiles de clientes  
Cada cliente estará definido por las siguientes propiedades:  

- **CUSTOMER_ID**: El ID único del cliente.  
- **(x_customer_id, y_customer_id)**: Un par de coordenadas reales **(x_customer_id, y_customer_id)** en una cuadrícula de 100x100, que define la ubicación geográfica del cliente.  
- **(mean_amount, std_amount)**: La media y la desviación estándar de los montos de las transacciones para el cliente, asumiendo que los montos de las transacciones siguen una distribución normal. El **mean_amount** se extraerá de una distribución uniforme en el intervalo (5, 100), y el **std_amount** se establecerá como el **mean_amount** dividido por dos.  
- **mean_nb_tx_per_day**: El número promedio de transacciones por día para el cliente, asumiendo que el número de transacciones por día sigue una distribución de Poisson. Este número se extraerá de una distribución uniforme en el intervalo (0, 4).  

La función **generate_customer_profiles_table** proporciona una implementación para generar una tabla de perfiles de clientes. Toma como entrada el número de clientes para los cuales generar un perfil y un estado aleatorio (**random state**) para garantizar la reproducibilidad. Devuelve un **DataFrame** que contiene las propiedades de cada cliente.  

### Proceso de generación de transacciones  
La simulación consistirá en cinco pasos principales:  

1. **Generación de perfiles de clientes**: Cada cliente es diferente en sus hábitos de gasto. Esto se simulará definiendo algunas propiedades para cada cliente. Las principales propiedades serán su ubicación geográfica, su frecuencia de gasto y los montos de sus transacciones. Las propiedades de los clientes se representarán en una tabla, denominada **tabla de perfiles de clientes**.  

2. **Generación de perfiles de terminales**: Las propiedades de los terminales consistirán simplemente en una ubicación geográfica. Las propiedades de los terminales se representarán en una tabla, denominada **tabla de perfiles de terminales**.  

3. **Asociación de perfiles de clientes a terminales**: Supondremos que los clientes solo realizan transacciones en terminales que se encuentran dentro de un radio de **r** de sus ubicaciones geográficas. Esto supone de manera simple que un cliente solo realiza transacciones en terminales que están geográficamente cerca de su ubicación. Este paso consistirá en agregar una característica llamada **list_terminals** a cada perfil de cliente, que contiene el conjunto de terminales que un cliente puede utilizar.  

4. **Generación de transacciones**: El simulador recorrerá el conjunto de perfiles de clientes y generará transacciones de acuerdo con sus propiedades (frecuencia de gasto, montos y terminales disponibles). Esto resultará en una tabla de transacciones.  

5. **Generación de escenarios de fraude**: Este último paso etiquetará las transacciones como legítimas o fraudulentas. Esto se hará siguiendo tres escenarios de fraude diferentes.  

El proceso de generación de transacciones se ilustra a continuación.  

![[Pasted image 20250307022848.png]]
**Fig. 2.** Proceso de generación de transacciones. Los perfiles de clientes y terminales se utilizan para generar un conjunto de transacciones. El paso final, que genera escenarios de fraude, proporciona la tabla de transacciones etiquetadas.  


Las siguientes secciones detallan la implementación de cada uno de estos pasos.  


```python
def generate_customer_profiles_table(n_customers, random_state=0):
    
    np.random.seed(random_state)
        
    customer_id_properties=[]
    
    # Generate customer properties from random distributions 
    for customer_id in range(n_customers):
        
        x_customer_id = np.random.uniform(0,100)
        y_customer_id = np.random.uniform(0,100)
        
        mean_amount = np.random.uniform(5,100) # Arbitrary (but sensible) value 
        std_amount = mean_amount/2 # Arbitrary (but sensible) value
        
        mean_nb_tx_per_day = np.random.uniform(0,4) # Arbitrary (but sensible) value 
        
        customer_id_properties.append([customer_id,
                                      x_customer_id, y_customer_id,
                                      mean_amount, std_amount,
                                      mean_nb_tx_per_day])
        
    customer_profiles_table = pd.DataFrame(customer_id_properties, columns=['CUSTOMER_ID',
                                                                      'x_customer_id', 'y_customer_id',
                                                                      'mean_amount', 'std_amount',
                                                                      'mean_nb_tx_per_day'])
    
    return customer_profiles_table
```

por ejemplo :

```python
n_customers = 5
customer_profiles_table = generate_customer_profiles_table(n_customers, random_state = 0)
customer_profiles_table
```

Ejemplo de salida:

|     | CUSTOMER_ID | x_customer_id | y_customer_id | mean_amount | std_amount | mean_nb_tx_per_day |
| --- | ----------- | ------------- | ------------- | ----------- | ---------- | ------------------ |
| 0   | 0           | 54.881350     | 71.518937     | 62.262521   | 31.131260  | 2.179533           |
| 1   | 1           | 42.365480     | 64.589411     | 46.570785   | 23.285393  | 3.567092           |
| 2   | 2           | 96.366276     | 38.344152     | 80.213879   | 40.106939  | 2.115580           |
| 3   | 3           | 56.804456     | 92.559664     | 11.748426   | 5.874213   | 0.348517           |
| 4   | 4           | 2.021840      | 83.261985     | 78.924891   | 39.462446  | 3.480049           |


### 2.2. Generación de perfiles de terminales

Cada terminal estará definido por las siguientes propiedades:

- **TERMINAL_ID**: El ID del terminal.
- **(x_terminal_id, y_terminal_id)**: Un par de coordenadas reales **(x_terminal_id, y_terminal_id)** en una cuadrícula de 100 * 100, que define la ubicación geográfica del terminal.

La función `generate_terminal_profiles_table` proporciona una implementación para generar una tabla de perfiles de terminales. Toma como entrada el número de terminales para los cuales se generará un perfil y un estado aleatorio para la reproducibilidad. Devuelve un DataFrame que contiene las propiedades de cada terminal.

```python
def generate_terminal_profiles_table(n_terminals, random_state=0):
    
    np.random.seed(random_state)
        
    terminal_id_properties=[]
    
    # Generate terminal properties from random distributions 
    for terminal_id in range(n_terminals):
        
        x_terminal_id = np.random.uniform(0,100)
        y_terminal_id = np.random.uniform(0,100)
        
        terminal_id_properties.append([terminal_id,
                                      x_terminal_id, y_terminal_id])
                                       
    terminal_profiles_table = pd.DataFrame(terminal_id_properties, columns=['TERMINAL_ID',
                                                                      'x_terminal_id', 'y_terminal_id'])
    
    return terminal_profiles_table
```

#### Ejemplo

A modo de ejemplo, generemos una tabla de terminales para cinco terminales:

```python
n_terminals = 5
terminal_profiles_table = generate_terminal_profiles_table(n_terminals, random_state = 0)
terminal_profiles_table
```


Ejemplo de salida:

|     | TERMINAL_ID | x_terminal_id | y_terminal_id |
| --- | ----------- | ------------- | ------------- |
| 0   | 0           | 54.881350     | 71.518937     |
| 1   | 1           | 60.276338     | 54.488318     |
| 2   | 2           | 42.365480     | 64.589411     |
| 3   | 3           | 43.758721     | 89.177300     |
| 4   | 4           | 96.366276     | 38.344152     |


### 2.3. Asociación de perfiles de clientes a terminales

Ahora asociemos los terminales con los perfiles de clientes. En nuestro diseño, los clientes solo pueden realizar transacciones en terminales que se encuentren dentro de un radio **r** de sus ubicaciones geográficas.

Primero, escribamos una función llamada `get_list_terminals_within_radius`, que encuentre estos terminales para un perfil de cliente. La función tomará como entrada:

- Un perfil de cliente (cualquier fila en la tabla de perfiles de clientes).
- Un arreglo que contiene la ubicación geográfica de todos los terminales.
- El radio **r**.

La función devolverá la lista de terminales que se encuentren dentro del radio **r** para ese cliente.

```python
def get_list_terminals_within_radius(customer_profile, x_y_terminals, r):
    
    # Use numpy arrays in the following to speed up computations
    
    # Location (x,y) of customer as numpy array
    x_y_customer = customer_profile[['x_customer_id','y_customer_id']].values.astype(float)
    
    # Squared difference in coordinates between customer and terminal locations
    squared_diff_x_y = np.square(x_y_customer - x_y_terminals)
    
    # Sum along rows and compute suared root to get distance
    dist_x_y = np.sqrt(np.sum(squared_diff_x_y, axis=1))
    
    # Get the indices of terminals which are at a distance less than r
    available_terminals = list(np.where(dist_x_y<r)[0])
    
    # Return the list of terminal IDs
    return available_terminals
```

#### Ejemplo

A modo de ejemplo, obtengamos la lista de terminales que se encuentran dentro de un radio **r = 50** del último cliente:

```output
[2, 3]
```

La lista contiene el tercer y cuarto terminal, que son, efectivamente, los únicos que se encuentran dentro de un radio de **50** del último cliente.

|     | TERMINAL_ID | x_terminal_id | y_terminal_id |
| --- | ----------- | ------------- | ------------- |
| 0   | 0           | 54.881350     | 71.518937     |
| 1   | 1           | 60.276338     | 54.488318     |
| 2   | 2           | 42.365480     | 64.589411     |
| 3   | 3           | 43.758721     | 89.177300     |
| 4   | 4           | 96.366276     | 38.344152     |


Para una mejor visualización, grafiquemos:

- Las ubicaciones de todos los terminales (en rojo).
- La ubicación del último cliente (en azul).
- La región dentro de un radio de **50** del último cliente (en verde).


```python
%%capture

terminals_available_to_customer_fig, ax = plt.subplots(figsize=(5,5))

# Plot locations of terminals
ax.scatter(terminal_profiles_table.x_terminal_id.values, 
           terminal_profiles_table.y_terminal_id.values, 
           color='blue', label = 'Locations of terminals')

# Plot location of the last customer
customer_id=4
ax.scatter(customer_profiles_table.iloc[customer_id].x_customer_id, 
           customer_profiles_table.iloc[customer_id].y_customer_id, 
           color='red',label="Location of last customer")

ax.legend(loc = 'upper left', bbox_to_anchor=(1.05, 1))

# Plot the region within a radius of 50 of the last customer
circ = plt.Circle((customer_profiles_table.iloc[customer_id].x_customer_id,
                   customer_profiles_table.iloc[customer_id].y_customer_id), radius=50, color='g', alpha=0.2)
ax.add_patch(circ)

fontsize=15

ax.set_title("Green circle: \n Terminals within a radius of 50 \n of the last customer")
ax.set_xlim([0, 100])
ax.set_ylim([0, 100])
    
ax.set_xlabel('x_terminal_id', fontsize=fontsize)
ax.set_ylabel('y_terminal_id', fontsize=fontsize)
```

```python
terminals_available_to_customer_fig
```

![[Pasted image 20250308230921.png]]


Calcular la lista de terminales disponibles para cada cliente es entonces sencillo, utilizando la función `apply` de pandas. Almacenamos los resultados como una nueva columna llamada `available_terminals` en la tabla de perfiles de clientes.

```python
customer_profiles_table['available_terminals']=customer_profiles_table.apply(lambda x : get_list_terminals_within_radius(x, x_y_terminals=x_y_terminals, r=50), axis=1)
customer_profiles_table
```

|     | CUSTOMER_ID | x_customer_id | y_customer_id | mean_amount | std_amount | mean_nb_tx_per_day | available_terminals |
| --- | ----------- | ------------- | ------------- | ----------- | ---------- | ------------------ | ------------------- |
| 0   | 0           | 54.881350     | 71.518937     | 62.262521   | 31.131260  | 2.179533           | [0, 1, 2, 3]        |
| 1   | 1           | 42.365480     | 64.589411     | 46.570785   | 23.285393  | 3.567092           | [0, 1, 2, 3]        |
| 2   | 2           | 96.366276     | 38.344152     | 80.213879   | 40.106939  | 2.115580           | [1, 4]              |
| 3   | 3           | 56.804456     | 92.559664     | 11.748426   | 5.874213   | 0.348517           | [0, 1, 2, 3]        |
| 4   | 4           | 2.021840      | 83.261985     | 78.924891   | 39.462446  | 3.480049           | [2, 3]              |

Vale la pena destacar que el radio **r** controla el número de terminales que, en promedio, estarán disponibles para cada cliente. A medida que se incrementa el número de terminales, este radio debe ajustarse para coincidir con el número promedio de terminales disponibles por cliente que se desea en una simulación.

### 2.4. Generación de transacciones

Los perfiles de clientes ahora contienen toda la información que necesitamos para generar transacciones. La generación de transacciones se realizará mediante una función llamada `generate_transactions_table`, que toma como entrada:

- Un perfil de cliente.
- Una fecha de inicio.
- Un número de días para los cuales generar transacciones.

La función devolverá una tabla de transacciones, que sigue el formato presentado anteriormente (sin la etiqueta de transacción, la cual se agregará en la generación de escenarios de fraude).

```python
def generate_transactions_table(customer_profile, start_date = "2018-04-01", nb_days = 10):
    
    customer_transactions = []
    
    random.seed(int(customer_profile.CUSTOMER_ID))
    np.random.seed(int(customer_profile.CUSTOMER_ID))
    
    # For all days
    for day in range(nb_days):
        
        # Random number of transactions for that day 
        nb_tx = np.random.poisson(customer_profile.mean_nb_tx_per_day)
        
        # If nb_tx positive, let us generate transactions
        if nb_tx>0:
            
            for tx in range(nb_tx):
                
                # Time of transaction: Around noon, std 20000 seconds. This choice aims at simulating the fact that 
                # most transactions occur during the day.
                time_tx = int(np.random.normal(86400/2, 20000))
                
                # If transaction time between 0 and 86400, let us keep it, otherwise, let us discard it
                if (time_tx>0) and (time_tx<86400):
                    
                    # Amount is drawn from a normal distribution  
                    amount = np.random.normal(customer_profile.mean_amount, customer_profile.std_amount)
                    
                    # If amount negative, draw from a uniform distribution
                    if amount<0:
                        amount = np.random.uniform(0,customer_profile.mean_amount*2)
                    
                    amount=np.round(amount,decimals=2)
                    
                    if len(customer_profile.available_terminals)>0:
                        
                        terminal_id = random.choice(customer_profile.available_terminals)
                    
                        customer_transactions.append([time_tx+day*86400, day,
                                                      customer_profile.CUSTOMER_ID, 
                                                      terminal_id, amount])
            
    customer_transactions = pd.DataFrame(customer_transactions, columns=['TX_TIME_SECONDS', 'TX_TIME_DAYS', 'CUSTOMER_ID', 'TERMINAL_ID', 'TX_AMOUNT'])
    
    if len(customer_transactions)>0:
        customer_transactions['TX_DATETIME'] = pd.to_datetime(customer_transactions["TX_TIME_SECONDS"], unit='s', origin=start_date)
        customer_transactions=customer_transactions[['TX_DATETIME','CUSTOMER_ID', 'TERMINAL_ID', 'TX_AMOUNT','TX_TIME_SECONDS', 'TX_TIME_DAYS']]
    
    return customer_transactions  
```

Generemos, por ejemplo, transacciones para el primer cliente, durante cinco días, comenzando en la fecha **2018-04-01**:

```python
transaction_table_customer_0=generate_transactions_table(customer_profiles_table.iloc[0], 
                                                         start_date = "2018-04-01", 
                                                         nb_days = 5)
transaction_table_customer_0
```


|     |     *TX_DATETIME*     | *CUSTOMER_ID* | *TERMINAL_ID* | *TX_AMOUNT* | *TX_TIME_SECONDS* | *TX_TIME_DAYS* |
| :-: | :-----------------: | :---------: | :---------: | :-------: | :-------------: | :----------: |
|  *0*  | *2018-04-01 07:19:05* |      *0*      |      *3*      |  *123.59*   |      *26345*      |      *0*       |
|  *1*  | *2018-04-01 19:02:02* |      *0*      |      *3*      |   *46.51*   |      *68522*      |      *0*       |
|  *2*  | *2018-04-01 18:00:16* |      *0*      |      *0*      |   *77.34*   |      *64816*      |      *0*       |
|  *3*  | *2018-04-02 15:13:02* |      *0*      |      *2*      |   *32.35*   |     *141182*      |      *1*       |
|  *4*  | *2018-04-02 14:05:38* |      *0*      |      *3*      |   *63.30*   |     *137138*      |      *1*       |
|  *5*  | *2018-04-02 15:46:51* |      *0*      |      *3*      |   *13.59*   |     *143211*      |      *1*       |
|  *6*  | *2018-04-02 08:51:06* |      *0*      |      *2*      |   *54.72*   |     *118266*      |      *1*       |
|  *7*  | *2018-04-02 20:24:47* |      *0*      |      *3*      |   *51.89*   |     *159887*      |      *1*       |



Podemos verificar rápidamente que las transacciones generadas siguen las propiedades del perfil del cliente:

- Los IDs de los terminales son, efectivamente, aquellos en la lista de terminales disponibles (0, 1, 2 y 3).
- Los montos de las transacciones parecen seguir los parámetros de monto del cliente (`mean_amount=62.26` y `std_amount=31.13`).
- El número de transacciones por día varía según los parámetros de frecuencia de transacciones del cliente (`mean_nb_tx_per_day=2.18`).

Ahora generemos las transacciones para todos los clientes. Esto es sencillo utilizando los métodos `groupby` y `apply` de pandas:

```python
transactions_df=customer_profiles_table.groupby('CUSTOMER_ID').apply(lambda x : generate_transactions_table(x.iloc[0], nb_days=5)).reset_index(drop=True)
transactions_df
```

|     | TX_DATETIME         | CUSTOMER_ID | TERMINAL_ID | TX_AMOUNT | TX_TIME_SECONDS | TX_TIME_DAYS |
| --- | ------------------- | ----------- | ----------- | --------- | --------------- | ------------ |
| 0   | 2018-04-01 07:19:05 | 0           | 3           | 123.59    | 26345           | 0            |
| 1   | 2018-04-01 19:02:02 | 0           | 3           | 46.51     | 68522           | 0            |
| 2   | 2018-04-01 18:00:16 | 0           | 0           | 77.34     | 64816           | 0            |
| 3   | 2018-04-02 15:13:02 | 0           | 2           | 32.35     | 141182          | 1            |
| 4   | 2018-04-02 14:05:38 | 0           | 3           | 63.30     | 137138          | 1            |
| ... | ...                 | ...         | ...         | ...       | ...             | ...          |
| 60  | 2018-04-05 07:41:19 | 4           | 2           | 111.38    | 373279          | 4            |
| 61  | 2018-04-05 06:59:59 | 4           | 3           | 80.36     | 370799          | 4            |
| 62  | 2018-04-05 17:23:34 | 4           | 2           | 53.25     | 408214          | 4            |
| 63  | 2018-04-05 12:51:38 | 4           | 2           | 36.44     | 391898          | 4            |
| 64  | 2018-04-05 12:38:46 | 4           | 3           | 17.53     | 391126          | 4            |

Esto nos da un conjunto de **65 transacciones**, con **5 clientes**, **5 terminales** y **5 días**.

### Escalando a un conjunto de datos más grande

Ahora tenemos todos los componentes necesarios para generar un conjunto de datos más grande. Escribamos una función `generate_dataset` que se encargue de ejecutar todos los pasos anteriores. Esta función:

- Tomará como entradas el número deseado de clientes, terminales y días, así como la fecha de inicio y el radio **r**.
- Devolverá las tablas generadas de perfiles de clientes y terminales, y el DataFrame de transacciones.

**Nota:**

Para acelerar los cálculos, se puede utilizar la función `parallel_apply` del módulo `pandarallel`. Esta función reemplaza la función `apply` de pandas y permite distribuir el cálculo en todas las CPU disponibles.


```python
def generate_dataset(n_customers = 10000, n_terminals = 1000000, nb_days=90, start_date="2018-04-01", r=5):
    
    start_time=time.time()
    customer_profiles_table = generate_customer_profiles_table(n_customers, random_state = 0)
    print("Time to generate customer profiles table: {0:.2}s".format(time.time()-start_time))
    
    start_time=time.time()
    terminal_profiles_table = generate_terminal_profiles_table(n_terminals, random_state = 1)
    print("Time to generate terminal profiles table: {0:.2}s".format(time.time()-start_time))
    
    start_time=time.time()
    x_y_terminals = terminal_profiles_table[['x_terminal_id','y_terminal_id']].values.astype(float)
    customer_profiles_table['available_terminals'] = customer_profiles_table.apply(lambda x : get_list_terminals_within_radius(x, x_y_terminals=x_y_terminals, r=r), axis=1)
    # With Pandarallel
    #customer_profiles_table['available_terminals'] = customer_profiles_table.parallel_apply(lambda x : get_list_closest_terminals(x, x_y_terminals=x_y_terminals, r=r), axis=1)
    customer_profiles_table['nb_terminals']=customer_profiles_table.available_terminals.apply(len)
    print("Time to associate terminals to customers: {0:.2}s".format(time.time()-start_time))
    
    start_time=time.time()
    transactions_df=customer_profiles_table.groupby('CUSTOMER_ID').apply(lambda x : generate_transactions_table(x.iloc[0], nb_days=nb_days)).reset_index(drop=True)
    # With Pandarallel
    #transactions_df=customer_profiles_table.groupby('CUSTOMER_ID').parallel_apply(lambda x : generate_transactions_table(x.iloc[0], nb_days=nb_days)).reset_index(drop=True)
    print("Time to generate transactions: {0:.2}s".format(time.time()-start_time))
    
    # Sort transactions chronologically
    transactions_df=transactions_df.sort_values('TX_DATETIME')
    # Reset indices, starting from 0
    transactions_df.reset_index(inplace=True,drop=True)
    transactions_df.reset_index(inplace=True)
    # TRANSACTION_ID are the dataframe indices, starting from 0
    transactions_df.rename(columns = {'index':'TRANSACTION_ID'}, inplace = True)
    
    return (customer_profiles_table, terminal_profiles_table, transactions_df)
```

Generemos un conjunto de datos que incluya:

- **5000 clientes**
- **10000 terminales**
- **183 días de transacciones** (lo que corresponde a un período simulado desde el **2018/04/01** hasta el **2018/09/30**)

La fecha de inicio se fija arbitrariamente en **2018/04/01**. El radio **r** se establece en **5**, lo que corresponde a aproximadamente **100 terminales disponibles** para cada cliente.

Generar este conjunto de datos toma alrededor de **3 minutos** en una computadora portátil estándar.

```python
(customer_profiles_table, terminal_profiles_table, transactions_df)=\
    generate_dataset(n_customers = 5000, 
                     n_terminals = 10000, 
                     nb_days=183, 
                     start_date="2018-04-01", 
                     r=5)
```

**Nota:**

Este número es bajo en comparación con los sistemas de detección de fraude del mundo real, donde pueden ser necesarios procesar millones de transacciones cada día. Sin embargo, este tamaño será suficiente para los propósitos de este libro, en particular para mantener tiempos de ejecución razonables.

```python
transactions_df
```

|         | TRANSACTION_ID | TX_DATETIME         | CUSTOMER_ID | TERMINAL_ID | TX_AMOUNT | TX_TIME_SECONDS | TX_TIME_DAYS |
| ------- | -------------- | ------------------- | ----------- | ----------- | --------- | --------------- | ------------ |
| 0       | 0              | 2018-04-01 00:00:31 | 596         | 3156        | 57.16     | 31              | 0            |
| 1       | 1              | 2018-04-01 00:02:10 | 4961        | 3412        | 81.51     | 130             | 0            |
| 2       | 2              | 2018-04-01 00:07:56 | 2           | 1365        | 146.00    | 476             | 0            |
| 3       | 3              | 2018-04-01 00:09:29 | 4128        | 8737        | 64.49     | 569             | 0            |
| 4       | 4              | 2018-04-01 00:10:34 | 927         | 9906        | 50.99     | 634             | 0            |
| ...     | ...            | ...                 | ...         | ...         | ...       | ...             | ...          |
| 1754150 | 1754150        | 2018-09-30 23:56:36 | 161         | 655         | 54.24     | 15810996        | 182          |
| 1754151 | 1754151        | 2018-09-30 23:57:38 | 4342        | 6181        | 1.23      | 15811058        | 182          |
| 1754152 | 1754152        | 2018-09-30 23:58:21 | 618         | 1502        | 6.62      | 15811101        | 182          |
| 1754153 | 1754153        | 2018-09-30 23:59:52 | 4056        | 3067        | 55.40     | 15811192        | 182          |
| 1754154 | 1754154        | 2018-09-30 23:59:57 | 3542        | 9849        | 23.59     | 15811197        | 182          |


Como verificación de coherencia, grafiquemos la distribución de los montos de las transacciones y los tiempos de las transacciones.

```python
%%capture

distribution_amount_times_fig, ax = plt.subplots(1, 2, figsize=(18,4))

amount_val = transactions_df[transactions_df.TX_TIME_DAYS<10]['TX_AMOUNT'].sample(n=10000).values
time_val = transactions_df[transactions_df.TX_TIME_DAYS<10]['TX_TIME_SECONDS'].sample(n=10000).values

sns.distplot(amount_val, ax=ax[0], color='r', hist = True, kde = False)
ax[0].set_title('Distribution of transaction amounts', fontsize=14)
ax[0].set_xlim([min(amount_val), max(amount_val)])
ax[0].set(xlabel = "Amount", ylabel="Number of transactions")

# We divide the time variables by 86400 to transform seconds to days in the plot
sns.distplot(time_val/86400, ax=ax[1], color='b', bins = 100, hist = True, kde = False)
ax[1].set_title('Distribution of transaction times', fontsize=14)
ax[1].set_xlim([min(time_val/86400), max(time_val/86400)])
ax[1].set_xticks(range(10))
ax[1].set(xlabel = "Time (days)", ylabel="Number of transactions")
```


```python
distribution_amount_times_fig
```


![[Pasted image 20250309014134.png]]

La distribución de los montos de las transacciones tiene la mayor parte de su masa en montos pequeños. La distribución de los tiempos de las transacciones sigue una distribución gaussiana en una base diaria, centrada alrededor del mediodía. Estas dos distribuciones están en concordancia con los parámetros de simulación utilizados en las secciones anteriores.


### 2.5. Generación de escenarios de fraude

Este último paso de la simulación agrega transacciones fraudulentas al conjunto de datos, utilizando los siguientes escenarios de fraude:

#### Escenario 1:
Cualquier transacción cuyo monto sea superior a **220** se considera fraudulenta. Este escenario no está inspirado en un caso del mundo real, sino que proporcionará un patrón de fraude obvio que debería ser detectado por cualquier detector de fraude básico. Esto será útil para validar la implementación de una técnica de detección de fraude.

#### Escenario 2:
Cada día, se selecciona al azar una lista de dos terminales. Todas las transacciones en estos terminales durante los próximos **28 días** se marcarán como fraudulentas. Este escenario simula el uso criminal de un terminal, por ejemplo, a través de phishing. Detectar este escenario será posible agregando características que rastreen el número de transacciones fraudulentas en el terminal. Dado que el terminal solo está comprometido durante **28 días**, se necesitarán estrategias adicionales que involucren el cambio de concepto (*concept drift*) para manejar eficientemente este escenario.

#### Escenario 3:
Cada día, se selecciona al azar una lista de **3 clientes**. En los próximos **14 días**, **1/3** de sus transacciones tendrán sus montos multiplicados por **5** y se marcarán como fraudulentas. Este escenario simula un fraude donde no se requiere la presencia física de la tarjeta (*card-not-present*), y las credenciales de un cliente han sido filtradas. El cliente continúa realizando transacciones, mientras que el estafador realiza transacciones de mayor valor para maximizar sus ganancias. Detectar este escenario requerirá agregar características que rastreen los hábitos de gasto del cliente. Al igual que en el Escenario 2, dado que la tarjeta solo está comprometida temporalmente, también se deberán diseñar estrategias adicionales que involucren el cambio de concepto (*concept drift*).

```python

def add_frauds(customer_profiles_table, terminal_profiles_table, transactions_df):
    
    # By default, all transactions are genuine
    transactions_df['TX_FRAUD']=0
    transactions_df['TX_FRAUD_SCENARIO']=0
    
    # Scenario 1
    transactions_df.loc[transactions_df.TX_AMOUNT>220, 'TX_FRAUD']=1
    transactions_df.loc[transactions_df.TX_AMOUNT>220, 'TX_FRAUD_SCENARIO']=1
    nb_frauds_scenario_1=transactions_df.TX_FRAUD.sum()
    print("Number of frauds from scenario 1: "+str(nb_frauds_scenario_1))
    
    # Scenario 2
    for day in range(transactions_df.TX_TIME_DAYS.max()):
        
        compromised_terminals = terminal_profiles_table.TERMINAL_ID.sample(n=2, random_state=day)
        
        compromised_transactions=transactions_df[(transactions_df.TX_TIME_DAYS>=day) & 
                                                    (transactions_df.TX_TIME_DAYS<day+28) & 
                                                    (transactions_df.TERMINAL_ID.isin(compromised_terminals))]
                            
        transactions_df.loc[compromised_transactions.index,'TX_FRAUD']=1
        transactions_df.loc[compromised_transactions.index,'TX_FRAUD_SCENARIO']=2
    
    nb_frauds_scenario_2=transactions_df.TX_FRAUD.sum()-nb_frauds_scenario_1
    print("Number of frauds from scenario 2: "+str(nb_frauds_scenario_2))
    
    # Scenario 3
    for day in range(transactions_df.TX_TIME_DAYS.max()):
        
        compromised_customers = customer_profiles_table.CUSTOMER_ID.sample(n=3, random_state=day).values
        
        compromised_transactions=transactions_df[(transactions_df.TX_TIME_DAYS>=day) & 
                                                    (transactions_df.TX_TIME_DAYS<day+14) & 
                                                    (transactions_df.CUSTOMER_ID.isin(compromised_customers))]
        
        nb_compromised_transactions=len(compromised_transactions)
        
        
        random.seed(day)
        index_fauds = random.sample(list(compromised_transactions.index.values),k=int(nb_compromised_transactions/3))
        
        transactions_df.loc[index_fauds,'TX_AMOUNT']=transactions_df.loc[index_fauds,'TX_AMOUNT']*5
        transactions_df.loc[index_fauds,'TX_FRAUD']=1
        transactions_df.loc[index_fauds,'TX_FRAUD_SCENARIO']=3
        
                             
    nb_frauds_scenario_3=transactions_df.TX_FRAUD.sum()-nb_frauds_scenario_2-nb_frauds_scenario_1
    print("Number of frauds from scenario 3: "+str(nb_frauds_scenario_3))
    
    return transactions_df                 
```


Agreguemos transacciones fraudulentas utilizando estos escenarios:

```python
%time transactions_df = add_frauds(customer_profiles_table, terminal_profiles_table, transactions_df)
```


Porcentaje de transacciones fraudulentas:
```output
0.008369271814634397
```

Número de transacciones fraudulentas:

```output
14681
```

Un total de **14,681 transacciones** fueron marcadas como fraudulentas. Esto representa el **0.8%** de las transacciones. Cabe destacar que la suma de los fraudes de cada escenario no es igual al número total de transacciones fraudulentas. Esto se debe a que algunas transacciones pueden haber sido marcadas como fraudulentas por dos o más escenarios de fraude.

Nuestro conjunto de datos de transacciones simuladas ahora está completo, con una etiqueta de fraude agregada a todas las transacciones.

```python
transactions_df.head()
```

Verifiquemos cómo varían diariamente el número de transacciones, el número de transacciones fraudulentas y el número de tarjetas comprometidas.

```python
def get_stats(transactions_df):
    #Number of transactions per day
    nb_tx_per_day=transactions_df.groupby(['TX_TIME_DAYS'])['CUSTOMER_ID'].count()
    #Number of fraudulent transactions per day
    nb_fraud_per_day=transactions_df.groupby(['TX_TIME_DAYS'])['TX_FRAUD'].sum()
    #Number of fraudulent cards per day
    nb_fraudcard_per_day=transactions_df[transactions_df['TX_FRAUD']>0].groupby(['TX_TIME_DAYS']).CUSTOMER_ID.nunique()
    
    return (nb_tx_per_day,nb_fraud_per_day,nb_fraudcard_per_day)

(nb_tx_per_day,nb_fraud_per_day,nb_fraudcard_per_day)=get_stats(transactions_df)

n_days=len(nb_tx_per_day)
tx_stats=pd.DataFrame({"value":pd.concat([nb_tx_per_day/50,nb_fraud_per_day,nb_fraudcard_per_day])})
tx_stats['stat_type']=["nb_tx_per_day"]*n_days+["nb_fraud_per_day"]*n_days+["nb_fraudcard_per_day"]*n_days
tx_stats=tx_stats.reset_index()
```


```python
%%capture

sns.set(style='darkgrid')
sns.set(font_scale=1.4)

fraud_and_transactions_stats_fig = plt.gcf()

fraud_and_transactions_stats_fig.set_size_inches(15, 8)

sns_plot = sns.lineplot(x="TX_TIME_DAYS", y="value", data=tx_stats, hue="stat_type", hue_order=["nb_tx_per_day","nb_fraud_per_day","nb_fraudcard_per_day"], legend=False)

sns_plot.set_title('Total transactions, and number of fraudulent transactions \n and number of compromised cards per day', fontsize=20)
sns_plot.set(xlabel = "Number of days since beginning of data generation", ylabel="Number")

sns_plot.set_ylim([0,300])

labels_legend = ["# transactions per day (/50)", "# fraudulent txs per day", "# fraudulent cards per day"]

sns_plot.legend(loc='upper left', labels=labels_legend,bbox_to_anchor=(1.05, 1), fontsize=15)
```

```python
fraud_and_transactions_stats_fig
```

![[Pasted image 20250309022243.png]]


Esta simulación generó alrededor de **10,000 transacciones por día**. El número de transacciones fraudulentas por día es de aproximadamente **85**, y el número de tarjetas fraudulentas ronda las **80**. Vale la pena destacar que el primer mes tiene un número menor de transacciones fraudulentas, lo cual se debe a que los fraudes de los escenarios 2 y 3 abarcan períodos de **28** y **14 días**, respectivamente.

El conjunto de datos resultante es interesante: presenta un **desequilibrio de clases** (menos del **1%** de transacciones fraudulentas), una mezcla de características numéricas y categóricas, relaciones no triviales entre las características y escenarios de fraude dependientes del tiempo.

Finalmente, guardemos el conjunto de datos para reutilizarlo en el resto de este libro.


### 2.6. Guardado del conjunto de datos

En lugar de guardar todo el conjunto de datos de transacciones, dividimos el conjunto de datos en lotes diarios. Esto permitirá cargar períodos específicos en lugar de todo el conjunto de datos. Se utiliza el formato **pickle**, en lugar de CSV, para acelerar los tiempos de carga. Todos los archivos se guardan en la carpeta **DIR_OUTPUT**. Los nombres de los archivos son las fechas, con la extensión **.pkl**.


```python
DIR_OUTPUT = "./simulated-data-raw/"

if not os.path.exists(DIR_OUTPUT):
    os.makedirs(DIR_OUTPUT)

start_date = datetime.datetime.strptime("2018-04-01", "%Y-%m-%d")

for day in range(transactions_df.TX_TIME_DAYS.max()+1):
    
    transactions_day = transactions_df[transactions_df.TX_TIME_DAYS==day].sort_values('TX_TIME_SECONDS')
    
    date = start_date + datetime.timedelta(days=day)
    filename_output = date.strftime("%Y-%m-%d")+'.pkl'
    
    # Protocol=4 required for Google Colab
    transactions_day.to_pickle(DIR_OUTPUT+filename_output, protocol=4)
```


### 3. Transformación de características base

El conjunto de datos simulado generado en la sección anterior es simple. Solo contiene las características esenciales que caracterizan una transacción con tarjeta de pago. Estas son: un identificador único para la transacción, la fecha y hora de la transacción, el monto de la transacción, un identificador único para el cliente, un número único para el comerciante y una variable binaria que etiqueta la transacción como legítima o fraudulenta (0 para legítima o 1 para fraudulenta). La Figura 1 muestra las primeras tres filas del conjunto de datos simulado:

![[Pasted image 20250309140912.png]]

**Figura 1.** Las primeras tres transacciones en el conjunto de datos simulado utilizado en este capítulo.


Cada fila resume esencialmente que, a las **00:00:31** del **1 de abril de 2018**, un cliente con ID **596** realizó un pago de **57.19** a un comerciante con ID **3156**, y que la transacción no fue fraudulenta. Luego, a las **00:02:10** del **1 de abril de 2018**, un cliente con ID **4961** realizó un pago de **81.51** a un comerciante con ID **3412**, y que la transacción no fue fraudulenta. Y así sucesivamente. El conjunto de datos simulado es una larga lista de este tipo de transacciones (**1.8 millones** en total). La variable **transaction_ID** es un identificador único para cada transacción.

Aunque conceptualmente simple para un humano, este conjunto de características no es adecuado para un modelo predictivo de aprendizaje automático. Los algoritmos de aprendizaje automático generalmente requieren características numéricas y ordenadas. **Numérico** significa que el tipo de variable debe ser un número entero o real. **Ordenado** significa que el orden de los valores de una variable es significativo.

En este conjunto de datos, las únicas características numéricas y ordenadas son el monto de la transacción y la etiqueta de fraude. La fecha es un tipo **timestamp** de Pandas y, por lo tanto, no es numérica. Los identificadores de transacciones, clientes y terminales son numéricos pero no ordenados: no tendría sentido asumir, por ejemplo, que el terminal con ID **3548** es "más grande" o "mayor" que el terminal con ID **1983**. Más bien, estos identificadores representan "entidades" distintas, conocidas como características categóricas.

Desafortunadamente, no existe un procedimiento estándar para manejar características no numéricas o categóricas. Este tema se conoce en la literatura de aprendizaje automático como **ingeniería de características** o **transformación de características**. En esencia, el objetivo de la ingeniería de características es diseñar nuevas características que se asuman relevantes para un problema predictivo. El diseño de estas características suele depender del problema e involucra conocimiento del dominio.

En esta sección, implementaremos tres tipos de transformación de características que se sabe son relevantes para la detección de fraude con tarjetas de pago.

![[Pasted image 20250309141432.png]]


El primer tipo de transformación involucra la variable de fecha/hora y consiste en crear características binarias que caracterizan períodos potencialmente relevantes. Crearemos dos de estas características. La primera caracterizará si una transacción ocurre durante un día laboral o durante el fin de semana. La segunda caracterizará si una transacción ocurre durante el día o la noche. Estas características pueden ser útiles, ya que se ha observado en conjuntos de datos del mundo real que los patrones de fraude difieren entre los días laborales y los fines de semana, y entre el día y la noche.

El segundo tipo de transformación involucra el ID del cliente y consiste en crear características que describen los comportamientos de gasto del cliente. Seguiremos el marco **RFM** (*Recency, Frequency, Monetary value*) propuesto en [VVBC+15], y realizaremos un seguimiento del monto promedio de gasto y el número de transacciones para cada cliente en tres ventanas de tiempo. Esto dará lugar a la creación de seis nuevas características.

El tercer tipo de transformación involucra el ID del terminal y consiste en crear nuevas características que describen el "riesgo" asociado con el terminal. El riesgo se definirá como el número promedio de fraudes observados en el terminal para tres ventanas de tiempo. Esto dará lugar a la creación de tres nuevas características.

La siguiente tabla resume los tipos de transformación que se realizarán y las nuevas características que se crearán:


| Nombre de la característica original | Tipo de característica original | Transformación                                                                                                                                                                                                                                                      | Número de nuevas características | Tipo de nueva(s) característica(s) |
| ------------------------------------ | ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------- | ---------------------------------- |
| TX_DATE_TIME                         | Timestamp de Pandas             | 0 si la transacción ocurre en un día laboral, 1 si ocurre en un fin de semana. La nueva característica se llama **TX_DURING_WEEKEND**.                                                                                                                              | 1                                | Entero (0/1)                       |
| TX_DATE_TIME                         | Timestamp de Pandas             | 0 si la transacción ocurre entre las 6 a.m. y las 12 p.m., 1 si ocurre entre las 12 p.m. y las 6 a.m. La nueva característica se llama **TX_DURING_NIGHT**.                                                                                                         | 1                                | Entero (0/1)                       |
| CUSTOMER_ID                          | Variable categórica             | Número de transacciones realizadas por el cliente en los últimos **n** días, para **n** en {1, 7, 30}. Las nuevas características se llaman **CUSTOMER_ID_NB_TX_nDAY_WINDOW**.                                                                                      | 3                                | Entero                             |
| CUSTOMER_ID                          | Variable categórica             | Monto promedio de gasto en los últimos **n** días, para **n** en {1, 7, 30}. Las nuevas características se llaman **CUSTOMER_ID_AVG_AMOUNT_nDAY_WINDOW**.                                                                                                           | 3                                | Real                               |
| TERMINAL_ID                          | Variable categórica             | Número de transacciones en el terminal en los últimos **n+d** días, para **n** en {1, 7, 30} y **d=7**. El parámetro **d** se llama retraso y se discutirá más adelante en este cuaderno. Las nuevas características se llaman **TERMINAL_ID_NB_TX_nDAY_WINDOW**.   | 3                                | Entero                             |
| TERMINAL_ID                          | Variable categórica             | Número promedio de fraudes en el terminal en los últimos **n+d** días, para **n** en {1, 7, 30} y **d=7**. El parámetro **d** se llama retraso y se discutirá más adelante en este cuaderno. Las nuevas características se llaman **TERMINAL_ID_RISK_nDAY_WINDOW**. | 3                                | Real                               |


### 3.1. Carga del conjunto de datos

Primero, carguemos los datos de transacciones simulados en el cuaderno anterior. Cargaremos los archivos de transacciones desde abril hasta septiembre. Los archivos se pueden cargar utilizando la función `read_from_files` en el cuaderno de funciones compartidas. Esta función se colocó en este cuaderno porque se utilizará con frecuencia a lo largo de este libro.

La función toma como entrada la carpeta donde se encuentran los archivos de datos y las fechas que definen el período a cargar (entre `BEGIN_DATE` y `END_DATE`). Devuelve un DataFrame de transacciones. Las transacciones están ordenadas cronológicamente.


```python
# Load a set of pickle files, put them together in a single DataFrame, and order them by time

# It takes as input the folder DIR_INPUT where the files are stored, and the BEGIN_DATE and END_DATE

def read_from_files(DIR_INPUT, BEGIN_DATE, END_DATE):

    files = [os.path.join(DIR_INPUT, f) for f in os.listdir(DIR_INPUT) if f>=BEGIN_DATE+'.pkl' and f<=END_DATE+'.pkl']

  

    frames = []
    for f in files:
        df = pd.read_pickle(f)
        frames.append(df)
        del df

    df_final = pd.concat(frames)
    df_final=df_final.sort_values('TRANSACTION_ID')
    df_final.reset_index(drop=True,inplace=True)

    #  Note: -1 are missing values for real world data

    df_final=df_final.replace([-1],0)

    return df_final
```


```python
DIR_INPUT='./simulated-data-raw/data/' 

BEGIN_DATE = "2018-04-01"
END_DATE = "2018-09-30"

print("Load  files")
%time transactions_df=read_from_files(DIR_INPUT, BEGIN_DATE, END_DATE)
print("{0} transactions loaded, containing {1} fraudulent transactions".format(len(transactions_df),transactions_df.TX_FRAUD.sum()))
```


```python
transactions_df.head()
```


|     | TRANSACTION_ID |     TX_DATETIME     | CUSTOMER_ID | TERMINAL_ID | TX_AMOUNT | TX_TIME_SECONDS | TX_TIME_DAYS | TX_FRAUD | TX_FRAUD_SCENARIO |
| :-: | :------------: | :-----------------: | :---------: | :---------: | :-------: | :-------------: | :----------: | :------: | :---------------: |
|  0  |       0        | 2018-04-01 00:00:31 |     596     |    3156     |   57.16   |       31        |      0       |    0     |         0         |
|  1  |       1        | 2018-04-01 00:02:10 |    4961     |    3412     |   81.51   |       130       |      0       |    0     |         0         |
|  2  |       2        | 2018-04-01 00:07:56 |      2      |    1365     |  146.00   |       476       |      0       |    0     |         0         |
|  3  |       3        | 2018-04-01 00:09:29 |    4128     |    8737     |   64.49   |       569       |      0       |    0     |         0         |
|  4  |       4        | 2018-04-01 00:10:34 |     927     |    9906     |   50.99   |       634       |      0       |    0     |         0         |


### 3.2. Transformaciones de fecha y hora

Crearemos dos nuevas características binarias a partir de las fechas y horas de las transacciones:

1. La primera caracterizará si una transacción ocurre durante un día laboral (valor **0**) o un fin de semana (**1**), y se llamará **TX_DURING_WEEKEND**.

2. La segunda caracterizará si una transacción ocurre durante el día (**0**) o durante la noche (**1**). La noche se define como las horas entre las **12 p.m.** y las **6 a.m.**. Se llamará **TX_DURING_NIGHT**.

Para la característica **TX_DURING_WEEKEND**, definimos una función `is_weekend` que toma como entrada un timestamp de Pandas y devuelve **1** si la fecha es durante un fin de semana, o **0** en caso contrario. El objeto `timestamp` proporciona convenientemente la función `weekday` para ayudar en el cálculo de este valor.

```python
def is_weekend(tx_datetime):
    
    # Transform date into weekday (0 is Monday, 6 is Sunday)
    weekday = tx_datetime.weekday()
    # Binary value: 0 if weekday, 1 if weekend
    is_weekend = weekday>=5
    
    return int(is_weekend)
```

Es entonces sencillo calcular esta característica para todas las transacciones utilizando la función `apply` de Pandas.

```python
%time transactions_df['TX_DURING_WEEKEND']=transactions_df.TX_DATETIME.apply(is_weekend)
```

Seguimos la misma lógica para implementar la característica **TX_DURING_NIGHT**. Primero, una función `is_night` que toma como entrada un timestamp de Pandas y devuelve **1** si la hora es durante la noche, o **0** en caso contrario. El objeto `timestamp` proporciona convenientemente la propiedad `hour` para ayudar en el cálculo de este valor.

```python
def is_night(tx_datetime):
    
    # Get the hour of the transaction
    tx_hour = tx_datetime.hour
    # Binary value: 1 if hour less than 6, and 0 otherwise
    is_night = tx_hour<=6
    
    return int(is_night)
```


```python
%time transactions_df['TX_DURING_NIGHT']=transactions_df.TX_DATETIME.apply(is_night)
```

Verifiquemos que estas características se calcularon correctamente.

```python
transactions_df[transactions_df.TX_TIME_DAYS>=30]
```

|         | TRANSACTION_ID | TX_DATETIME         | CUSTOMER_ID | TERMINAL_ID | TX_AMOUNT | TX_TIME_SECONDS | TX_TIME_DAYS | TX_FRAUD | TX_FRAUD_SCENARIO | TX_DURING_WEEKEND | TX_DURING_NIGHT |
| ------- | -------------- | ------------------- | ----------- | ----------- | --------- | --------------- | ------------ | -------- | ----------------- | ----------------- | --------------- |
| 288062  | 288062         | 2018-05-01 00:01:21 | 3546        | 2944        | 18.71     | 2592081         | 30           | 0        | 0                 | 0                 | 1               |
| 288063  | 288063         | 2018-05-01 00:01:48 | 206         | 3521        | 18.60     | 2592108         | 30           | 0        | 0                 | 0                 | 1               |
| 288064  | 288064         | 2018-05-01 00:02:22 | 2610        | 4470        | 66.67     | 2592142         | 30           | 0        | 0                 | 0                 | 1               |
| 288065  | 288065         | 2018-05-01 00:03:15 | 4578        | 1520        | 79.41     | 2592195         | 30           | 0        | 0                 | 0                 | 1               |
| 288066  | 288066         | 2018-05-01 00:03:51 | 1246        | 7809        | 52.08     | 2592231         | 30           | 0        | 0                 | 0                 | 1               |
| ...     | ...            | ...                 | ...         | ...         | ...       | ...             | ...          | ...      | ...               | ...               | ...             |
| 1754150 | 1754150        | 2018-09-30 23:56:36 | 161         | 655         | 54.24     | 15810996        | 182          | 0        | 0                 | 1                 | 0               |
| 1754151 | 1754151        | 2018-09-30 23:57:38 | 4342        | 6181        | 1.23      | 15811058        | 182          | 0        | 0                 | 1                 | 0               |
| 1754152 | 1754152        | 2018-09-30 23:58:21 | 618         | 1502        | 6.62      | 15811101        | 182          | 0        | 0                 | 1                 | 0               |
| 1754153 | 1754153        | 2018-09-30 23:59:52 | 4056        | 3067        | 55.40     | 15811192        | 182          | 0        | 0                 | 1                 | 0               |
| 1754154 | 1754154        | 2018-09-30 23:59:57 | 3542        | 9849        | 23.59     | 15811197        | 182          | 0        | 0                 | 1                 | 0               |

El **2018-05-01** fue un lunes, y el **2018-09-30** fue un domingo. Estas fechas están correctamente marcadas como día laboral y fin de semana, respectivamente. La característica de día y noche también está correctamente configurada para las primeras transacciones, que ocurren poco después de las **12 p.m.**, y las últimas transacciones, que ocurren poco antes de las **12 p.m.**.






#fraude #ml #ia
