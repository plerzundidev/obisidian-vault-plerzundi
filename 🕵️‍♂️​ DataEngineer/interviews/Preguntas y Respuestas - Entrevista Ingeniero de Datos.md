
## 1. FUNDAMENTOS DE BASES DE DATOS

### P: ¿Cuál es la diferencia entre OLTP y OLAP?

**R:**

- **OLTP (Online Transaction Processing):** Sistema optimizado para transacciones en tiempo real. Características: muchas transacciones pequeñas, alta concurrencia, normalización, baja latencia. Ejemplo: sistema de ventas, banca online.
- **OLAP (Online Analytical Processing):** Sistema optimizado para análisis y consultas complejas. Características: pocas consultas grandes, datos históricos, desnormalización, optimizado para lectura. Ejemplo: data warehouse, reportes ejecutivos.

### P: ¿Cuándo usarías normalización vs desnormalización?

**R:**

- **Normalización:** Para sistemas OLTP donde necesitas evitar redundancia, mantener integridad de datos y optimizar escrituras. Reduce el espacio de almacenamiento pero puede hacer consultas más lentas.
- **Desnormalización:** Para sistemas OLAP donde priorizas velocidad de consulta sobre espacio. Acepta redundancia para mejorar performance de lectura. Común en data warehouses y sistemas analíticos.

### P: Explica los tipos de JOIN en SQL

**R:**

- **INNER JOIN:** Devuelve solo registros que coinciden en ambas tablas
- **LEFT JOIN:** Devuelve todos los registros de la tabla izquierda + coincidencias de la derecha
- **RIGHT JOIN:** Devuelve todos los registros de la tabla derecha + coincidencias de la izquierda
- **FULL OUTER JOIN:** Devuelve todos los registros de ambas tablas
- **CROSS JOIN:** Producto cartesiano de ambas tablas

### P: ¿Qué son las Window Functions y cuándo las usarías?

**R:** Son funciones que operan sobre un conjunto de filas relacionadas con la fila actual, sin colapsar el resultado como GROUP BY. Ejemplos:

- **ROW_NUMBER():** Para ranking
- **LEAD/LAG:** Para comparar con filas anteriores/siguientes
- **SUM() OVER():** Para totales acumulativos Útiles para análisis de series temporales, rankings, y cálculos que requieren contexto de otras filas.

## 2. ARQUITECTURA DE DATOS

### P: ¿Cuál es la diferencia entre Data Lake, Data Warehouse, Data Mart y Data Lakehouse?

**R:**

**Data Lake:**

- **Definición:** Repositorio centralizado que almacena datos en su formato nativo/crudo
- **Características:** Schema-on-read, almacena datos estructurados, semi-estructurados y no estructurados
- **Ventajas:** Flexibilidad, bajo costo de almacenamiento, escalabilidad
- **Desventajas:** Puede convertirse en "data swamp", requiere governance
- **Casos de uso:**
    - Exploración de datos y data science
    - Backup y archiving de datos históricos
    - IoT data ingestion
    - Machine learning con datos diversos
- **Tecnologías:** AWS S3, Azure Data Lake, Google Cloud Storage, HDFS

**Data Warehouse:**

- **Definición:** Sistema optimizado para análisis y reporting con datos estructurados
- **Características:** Schema-on-write, datos limpios y transformados, optimizado para consultas
- **Ventajas:** Alta performance para queries, datos confiables, fácil de usar para analistas
- **Desventajas:** Menos flexible, costo más alto, solo datos estructurados
- **Casos de uso:**
    - Business intelligence y reporting
    - Dashboards ejecutivos
    - Análisis histórico y tendencias
    - Compliance y auditoría
- **Tecnologías:** Snowflake, Amazon Redshift, Google BigQuery, Azure Synapse

**Data Mart:**

- **Definición:** Subconjunto especializado de un data warehouse para un departamento específico
- **Características:** Dominio específico, datos pre-agregados, optimizado para usuarios finales
- **Ventajas:** Performance específica, fácil mantenimiento, acceso controlado
- **Desventajas:** Puede crear silos de datos, duplicación
- **Casos de uso:**
    - Reportes departamentales (ventas, marketing, finanzas)
    - KPIs específicos por área
    - Self-service analytics
    - Análisis de performance por dominio
- **Tecnologías:** Mismas que data warehouse pero con scope limitado

**Data Lakehouse:**

- **Definición:** Arquitectura que combina flexibilidad del data lake con performance del data warehouse
- **Características:** Schema-on-read con capacidades ACID, metadata layer, optimizaciones de consulta
- **Ventajas:** Flexibilidad + performance, elimina duplicación, soporte ML nativo
- **Desventajas:** Tecnología más nueva, complejidad de implementación
- **Casos de uso:**
    - Unified analytics (BI + ML)
    - Real-time + batch processing
    - Data science + business intelligence
    - Modernización de arquitecturas existentes
- **Tecnologías:** Databricks Delta Lake, Apache Iceberg, Apache Hudi

### P: ¿Cuándo elegirías cada arquitectura de datos?

**R:**

**Elige Data Lake cuando:**

- Tienes datos diversos (logs, IoT, social media, archivos)
- Necesitas experimentación y exploración de datos
- El budget es limitado para almacenamiento
- Los casos de uso no están claramente definidos
- Tienes equipos de data science que necesitan flexibilidad

**Elige Data Warehouse cuando:**

- Necesitas performance consistente para BI
- Los usuarios finales requieren datos confiables y rápidos
- Tienes casos de uso bien definidos y estructurados
- Compliance y auditoría son críticos
- Los analistas de negocio son los usuarios principales

**Elige Data Mart cuando:**

- Tienes departamentos con necesidades específicas
- Quieres optimizar performance para casos de uso particulares
- Necesitas control granular de acceso por dominio
- Tienes equipos que requieren autonomía en sus datos
- El data warehouse central es muy grande/complejo

**Elige Data Lakehouse cuando:**

- Necesitas tanto BI como ML en la misma plataforma
- Quieres eliminar silos entre data lake y data warehouse
- Tienes casos de uso de streaming y batch
- Necesitas governance pero también flexibilidad
- Quieres modernizar una arquitectura lambda existente

### P: ¿Cuál es la diferencia entre ETL y ELT?

**R:**

- **ETL (Extract, Transform, Load):** Transforma datos antes de cargarlos al destino. Mejor para transformaciones complejas y cuando el destino tiene recursos limitados.
- **ELT (Extract, Load, Transform):** Carga datos crudos primero, luego transforma. Aprovecha el poder de procesamiento del destino. Más común en cloud computing donde el almacenamiento es barato.

### P: ¿Cómo evolucionaría una arquitectura de datos en una empresa?

**R:**

**Fase 1 - Startup/Small Company:**

- Aplicación → Base de datos transaccional
- Reportes directos desde la DB de producción
- Herramientas: PostgreSQL + herramientas de BI básicas

**Fase 2 - Crecimiento:**

- Implementación de Data Warehouse básico
- ETL jobs para separar analytics de transaccional
- Herramientas: Cloud data warehouse + ETL tools

**Fase 3 - Diversificación:**

- Data Lake para datos no estructurados
- Múltiples fuentes de datos (APIs, logs, eventos)
- Data Marts por departamento
- Herramientas: S3 + Spark + departmental data marts

**Fase 4 - Madurez:**

- Data Lakehouse para unificar analytics y ML
- Real-time streaming + batch processing
- Advanced governance y data catalog
- Herramientas: Databricks/Snowflake + streaming platforms

**Fase 5 - Data-Driven Organization:**

- Mesh architecture con data products
- Self-service analytics democratizado
- MLOps integrado en el pipeline
- Herramientas: Distributed data platforms + AI/ML automation

### P: ¿Qué factores consideras para elegir entre tecnologías de almacenamiento?

**R:**

**Volumen de datos:**

- **< 1TB:** PostgreSQL, MySQL con réplicas de lectura
- **1TB - 100TB:** Cloud data warehouses (Snowflake, BigQuery)
- **> 100TB:** Data lakes (S3, ADLS) con compute separado

**Latencia requerida:**

- **Sub-segundo:** Redis, Memcached, DynamoDB
- **Segundos:** Elasticsearch, ClickHouse
- **Minutos:** Data warehouses tradicionales
- **Horas/Días:** Batch processing en data lakes

**Tipos de consultas:**

- **OLTP:** PostgreSQL, MySQL, SQL Server
- **OLAP:** Snowflake, BigQuery, Redshift
- **Search:** Elasticsearch, Solr
- **Graph:** Neo4j, Amazon Neptune
- **Time-series:** InfluxDB, TimescaleDB

**Presupuesto:**

- **Low cost:** Open source (PostgreSQL + S3)
- **Medium:** Cloud managed services
- **High performance:** Premium cloud services + optimization

**R:**

- **Batch Processing:** Para grandes volúmenes de datos donde la latencia no es crítica. Ejemplos: reportes diarios, análisis históricos, ML training. Tecnologías: Spark, Hadoop.
- **Stream Processing:** Para datos que requieren procesamiento en tiempo real. Ejemplos: detección de fraude, alertas, dashboards en vivo. Tecnologías: Kafka Streams, Apache Flink, Storm.

## 3. DATA MODELING

### P: ¿Cuáles son los tipos principales de data modeling y cuándo usar cada uno?

**R:**

**1. Conceptual Data Model:**

- **Propósito:** Vista de alto nivel de los datos del negocio
- **Características:** Entidades principales y relaciones, sin detalles técnicos
- **Audiencia:** Stakeholders de negocio, arquitectos
- **Cuándo usar:** Fase inicial de diseño, alineamiento con negocio

**2. Logical Data Model:**

- **Propósito:** Estructura detallada independiente de tecnología
- **Características:** Entidades, atributos, tipos de datos, relaciones, normalización
- **Audiencia:** Data architects, analistas
- **Cuándo usar:** Diseño detallado antes de implementación

**3. Physical Data Model:**

- **Propósito:** Implementación específica en una tecnología
- **Características:** Tablas, columnas, índices, particiones, constraints
- **Audiencia:** DBAs, desarrolladores
- **Cuándo usar:** Implementación real en base de datos

### P: ¿Qué es el modelado dimensional y cuáles son sus componentes?

**R:**

**Modelado Dimensional** es una técnica para diseñar data warehouses optimizada para consultas analíticas.

**Componentes principales:**

**1. Fact Tables (Tablas de Hechos):**

- Contienen métricas numéricas del negocio
- Granularidad específica (ej: venta por producto por día)
- Foreign keys hacia dimension tables
- Ejemplo: `sales_fact` con columns: date_key, product_key, customer_key, sales_amount, quantity

**2. Dimension Tables (Tablas de Dimensión):**

- Contienen atributos descriptivos
- Relatively static data
- Primary key que conecta con fact table
- Ejemplo: `product_dim` con columns: product_key, product_name, category, brand, price

**Tipos de Fact Tables:**

- **Transaction:** Una fila por transacción
- **Periodic Snapshot:** Estado en puntos específicos del tiempo
- **Accumulating Snapshot:** Proceso que cambia con el tiempo

### P: ¿Cuáles son los esquemas dimensionales más comunes?

**R:**

**1. Star Schema:**

- **Estructura:** Fact table central rodeada de dimension tables
- **Ventajas:** Simple, performance óptimo, fácil de entender
- **Desventajas:** Posible redundancia en dimensions
- **Cuándo usar:** Mayoría de casos, especialmente con herramientas BI modernas

```sql
-- Star Schema Example
FACT_SALES (date_key, product_key, customer_key, sales_amount)
DIM_DATE (date_key, date, month, quarter, year)
DIM_PRODUCT (product_key, name, category, brand)
DIM_CUSTOMER (customer_key, name, segment, region)
```

**2. Snowflake Schema:**

- **Estructura:** Dimension tables normalizadas en múltiples niveles
- **Ventajas:** Reduce redundancia, menor espacio de almacenamiento
- **Desventajas:** Más complejo, más JOINs, menor performance
- **Cuándo usar:** Cuando el espacio de almacenamiento es crítico

**3. Galaxy Schema (Constellation):**

- **Estructura:** Múltiples fact tables que comparten dimension tables
- **Ventajas:** Reutilización de dimensions, modelo más realista
- **Desventajas:** Mayor complejidad
- **Cuándo usar:** Múltiples procesos de negocio relacionados

### P: ¿Qué son las Slowly Changing Dimensions (SCD) y cómo las manejas?

**R:**

**SCD** es el manejo de cambios en dimension tables a lo largo del tiempo.

**Tipos de SCD:**

**Type 0 - Retain Original:**

- No se permiten cambios
- Datos históricos inmutables
- Ejemplo: Fecha de nacimiento de cliente

**Type 1 - Overwrite:**

- Sobrescribe el valor anterior
- No mantiene historia
- Ejemplo: Corrección de errores tipográficos

```sql
UPDATE customer_dim 
SET email = 'newemail@domain.com' 
WHERE customer_key = 123;
```

**Type 2 - Add New Record:**

- Mantiene historia completa
- Agrega nueva fila para cada cambio
- Usa effective_date, end_date, is_current flags

```sql
-- Cerrar registro anterior
UPDATE customer_dim 
SET end_date = CURRENT_DATE, is_current = FALSE 
WHERE customer_key = 123 AND is_current = TRUE;

-- Insertar nuevo registro
INSERT INTO customer_dim (customer_id, name, address, effective_date, is_current)
VALUES (123, 'John Doe', 'New Address', CURRENT_DATE, TRUE);
```

**Type 3 - Add New Attribute:**

- Mantiene valor anterior y actual
- Limitado a cambios poco frecuentes

```sql
ALTER TABLE customer_dim ADD COLUMN previous_address VARCHAR(255);
UPDATE customer_dim SET previous_address = address, address = 'New Address';
```

**Type 4 - History Table:**

- Tabla separada para mantener historia
- Current table + history table

**Type 6 - Hybrid (1+2+3):**

- Combina múltiples approaches

### P: ¿Qué es Data Vault y cuándo lo usarías?

**R:**

**Data Vault** es una metodología de modelado para enterprise data warehouses que prioriza auditoría, flexibilidad y escalabilidad.

**Componentes principales:**

**1. Hubs:**

- Entidades de negocio únicas
- Solo business keys y metadata
- Ejemplo: HUB_CUSTOMER con customer_business_key

**2. Links:**

- Relaciones entre hubs
- Transacciones y asociaciones
- Ejemplo: LINK_SALES conecta HUB_CUSTOMER y HUB_PRODUCT

**3. Satellites:**

- Atributos descriptivos y contextuales
- Historial de cambios
- Ejemplo: SAT_CUSTOMER_DETAILS con name, address, etc.

**Ventajas:**

- Auditoría completa y rastreabilidad
- Paralelización de desarrollo
- Flexibilidad para cambios de negocio
- Cumple con regulaciones de datos

**Desventajas:**

- Curva de aprendizaje alta
- Complejidad en consultas
- Overhead de almacenamiento

**Cuándo usar:**

- Entornos altamente regulados (banca, salud)
- Múltiples fuentes de datos complejas
- Requisitos de auditoría estrictos
- Equipos grandes de desarrollo

### P: ¿Cómo diseñarías el modelo de datos para un e-commerce?

**R:**

**Análisis de Requerimientos:**

- Productos con categorías y variantes
- Clientes con historial de compras
- Órdenes con múltiples items
- Inventario y pricing dinámico
- Reviews y ratings

**Modelo Dimensional - Star Schema:**

```sql
-- Fact Table
FACT_SALES (
    date_key,
    product_key,
    customer_key,
    order_key,
    quantity_sold,
    unit_price,
    discount_amount,
    total_amount,
    cost_of_goods
);

-- Dimension Tables
DIM_DATE (
    date_key,
    full_date,
    day_of_week,
    month,
    quarter,
    year,
    is_holiday
);

DIM_PRODUCT (
    product_key,
    product_id,
    product_name,
    category,
    subcategory,
    brand,
    supplier,
    current_price
);

DIM_CUSTOMER (
    customer_key,
    customer_id,
    customer_name,
    customer_segment,
    registration_date,
    country,
    city,
    age_group
);

DIM_ORDER (
    order_key,
    order_id,
    order_status,
    payment_method,
    shipping_method,
    promotion_code
);
```

**Consideraciones adicionales:**

- **SCD Type 2** para productos (cambios de precio, categoría)
- **Fact tables adicionales:** inventory_fact, customer_behavior_fact
- **Bridge tables** para many-to-many relationships
- **Junk dimensions** para flags y códigos de bajo cardinalidad

### P: ¿Cómo optimizarías el performance de un modelo dimensional?

**R:**

**1. Partitioning:**

```sql
-- Particionar fact table por fecha
CREATE TABLE fact_sales (...)
PARTITION BY RANGE (date_key) (
    PARTITION p2023 VALUES LESS THAN (20240101),
    PARTITION p2024 VALUES LESS THAN (20250101)
);
```

**2. Indexing Strategy:**

- **Clustered indexes** en date columns para fact tables
- **Non-clustered indexes** en foreign keys
- **Composite indexes** para consultas frecuentes

```sql
CREATE INDEX idx_sales_date_product ON fact_sales (date_key, product_key);
```

**3. Aggregation Tables:**

- Pre-calcular métricas comunes
- Monthly, quarterly summaries

```sql
CREATE TABLE fact_sales_monthly AS
SELECT date_key, product_key, SUM(sales_amount) as total_sales
FROM fact_sales 
GROUP BY date_key, product_key;
```

**4. Columnar Storage:**

- Usar formatos como Parquet
- Comprensión y columnar scanning
- Optimal para analytical workloads

**5. Materialized Views:**

- Para consultas complejas frecuentes
- Automatic refresh strategies

### P: ¿Qué es Apache Spark y cuáles son sus ventajas?

**R:** Framework de procesamiento distribuido para big data. Ventajas:

- **In-memory computing:** Más rápido que Hadoop MapReduce
- **Unified platform:** Batch, streaming, ML, graph processing
- **Multi-lenguaje:** Scala, Python, Java, R
- **Lazy evaluation:** Optimiza el plan de ejecución
- **Fault tolerance:** Recovery automático de fallos

### P: ¿Para qué sirve Apache Airflow?

**R:** Orquestador de workflows para programar y monitorear pipelines de datos. Características:

- **DAGs (Directed Acyclic Graphs):** Define dependencias entre tareas
- **Scheduling:** Ejecución basada en tiempo o eventos
- **Monitoring:** UI para ver estado de pipelines
- **Retry logic:** Manejo automático de fallos
- **Extensible:** Múltiples operadores y hooks

### P: ¿Qué es Apache Kafka y cuándo lo usarías?

**R:** Plataforma de streaming distribuida para mensajería en tiempo real. Casos de uso:

- **Event streaming:** Captura eventos en tiempo real
- **Data integration:** Conecta sistemas heterogéneos
- **Log aggregation:** Centraliza logs de múltiples servicios
- **Message queuing:** Comunicación asíncrona entre servicios Ventajas: alta throughput, durabilidad, escalabilidad horizontal.

## 5. PROGRAMACIÓN Y HERRAMIENTAS

### P: ¿Cuáles son las principales librerías de Python para Data Engineering?

**R:**

- **Pandas:** Manipulación y análisis de datos estructurados
- **NumPy:** Operaciones numéricas y arrays
- **SQLAlchemy:** ORM y conexión a bases de datos
- **Requests:** Consumo de APIs REST
- **PySpark:** Interface Python para Apache Spark
- **Apache Beam:** Procesamiento batch y stream unificado
- **Great Expectations:** Testing y validación de calidad de datos

### P: ¿Cómo manejarías APIs con rate limiting en Python?

**R:**

```python
import time
import requests
from requests.adapters import HTTPAdapter
from requests.packages.urllib3.util.retry import Retry

# Retry strategy
retry_strategy = Retry(
    total=3,
    backoff_factor=1,
    status_forcelist=[429, 500, 502, 503, 504]
)

# Rate limiting con sleep
def api_call_with_rate_limit(url, delay=1):
    response = requests.get(url)
    if response.status_code == 429:
        time.sleep(delay)
        return api_call_with_rate_limit(url, delay*2)
    return response
```

## 6. CALIDAD Y GOBERNANZA DE DATOS

### P: ¿Cómo implementarías data quality checks en un pipeline?

**R:**

1. **Schema validation:** Verificar tipos de datos y estructura
2. **Completeness:** Chequear valores nulos o faltantes
3. **Uniqueness:** Detectar duplicados
4. **Range checks:** Validar valores dentro de rangos esperados
5. **Referential integrity:** Verificar foreign keys
6. **Freshness:** Asegurar que los datos son recientes

Herramientas: Great Expectations, Apache Griffin, custom validators.

### P: ¿Qué es Data Lineage y por qué es importante?

**R:** Data Lineage traza el origen, movimiento y transformación de datos a través del pipeline. Importancia:

- **Troubleshooting:** Identifica origen de problemas de calidad
- **Impact analysis:** Entiende efectos de cambios upstream
- **Compliance:** Auditoría y regulaciones (GDPR)
- **Documentation:** Comprensión del flujo de datos
- **Root cause analysis:** Debug de problemas en producción

## 7. ESCENARIOS PRÁCTICOS

### P: Diseña un pipeline para procesar 1TB de datos diarios de logs de servidor

**R:**

1. **Ingestion:**
    
    - Kafka para streaming en tiempo real
    - Batch ingestion con cron jobs para archivos
2. **Storage:**
    
    - Raw data: S3/HDFS particionado por fecha
    - Processed data: Parquet para mejor compresión
3. **Processing:**
    
    - Spark para transformaciones batch
    - Particionamiento por fecha y servidor
    - Compresión y optimización de formatos
4. **Orchestration:**
    
    - Airflow DAG con dependencias
    - Monitoring y alertas automáticas
5. **Serving:**
    
    - Data warehouse para analytics
    - Cache para consultas frecuentes

### P: ¿Cómo manejarías datos duplicados en un dataset grande?

**R:**

1. **Identificación:**
    
    ```sql
    -- Encontrar duplicados
    SELECT column1, column2, COUNT(*)
    FROM table 
    GROUP BY column1, column2 
    HAVING COUNT(*) > 1
    ```
    
2. **Estrategias de eliminación:**
    
    - **Window functions:** `ROW_NUMBER() OVER (PARTITION BY key ORDER BY timestamp DESC)`
    - **Deduplication keys:** Definir combinación única de columnas
    - **Temporal logic:** Mantener registro más reciente
3. **Prevención:**
    
    - Primary keys en destino
    - Upsert operations (INSERT...ON CONFLICT)
    - Data quality checks en pipeline

### P: Sistema de recomendaciones: ¿cómo estructurarías el pipeline de datos?

**R:**

1. **Data Collection:**
    
    - User behavior: clicks, views, purchases
    - Product metadata: categories, descriptions, prices
    - Real-time events via Kafka
2. **Storage Architecture:**
    
    - Raw events: Data Lake (S3)
    - User profiles: NoSQL (DynamoDB)
    - Product catalog: SQL Database
    - Features: Feature store (Redis/Feast)
3. **Processing Pipeline:**
    
    - Batch: Spark jobs para feature engineering
    - Stream: Real-time feature updates
    - ML Pipeline: Training y serving models
4. **Serving Layer:**
    
    - API endpoints para recomendaciones
    - Caching para baja latencia
    - A/B testing framework

## 8. MONITOREO Y TROUBLESHOOTING

### P: ¿Cómo monitorearías la salud de un pipeline de datos?

**R:**

1. **Métricas técnicas:**
    
    - Latencia de procesamiento
    - Throughput (registros/segundo)
    - Error rates y tipos de errores
    - Resource utilization (CPU, memoria)
2. **Métricas de negocio:**
    
    - Data freshness
    - Completeness de datos
    - Accuracy vs baseline
    - SLA compliance
3. **Alerting:**
    
    - Thresholds dinámicos
    - Escalation policies
    - Integration con Slack/PagerDuty
4. **Herramientas:**
    
    - Prometheus + Grafana
    - DataDog, New Relic
    - Custom dashboards

### P: ¿Cómo tratarías una query gigante y compleja que está causando problemas de performance?

**R:**

**1. ANÁLISIS INICIAL:**

**Entender el contexto:**

- ¿Cuál es el propósito de la query?
- ¿Quién la usa y con qué frecuencia?
- ¿Cuál es el SLA esperado?
- ¿Cuántos datos procesa?

**Identificar el problema:**

```sql
-- Revisar execution plan
EXPLAIN (ANALYZE, BUFFERS) SELECT ...;

-- Identificar métricas clave
- Execution time
- Rows processed
- Memory usage
- I/O operations
- Table scans vs index usage
```

**2. ESTRATEGIAS DE OPTIMIZACIÓN:**

**A. Optimización de Query:**

```sql
-- ANTES: Query problemática
SELECT c.customer_name, p.product_name, 
       SUM(s.amount) as total_sales,
       AVG(s.amount) as avg_sales,
       COUNT(*) as transaction_count
FROM sales s
JOIN customers c ON s.customer_id = c.customer_id
JOIN products p ON s.product_id = p.product_id
WHERE s.sale_date BETWEEN '2023-01-01' AND '2023-12-31'
  AND c.customer_segment IN ('Premium', 'Gold')
  AND p.category = 'Electronics'
GROUP BY c.customer_name, p.product_name
HAVING SUM(s.amount) > 10000
ORDER BY total_sales DESC;

-- DESPUÉS: Query optimizada
WITH filtered_sales AS (
  SELECT customer_id, product_id, amount
  FROM sales 
  WHERE sale_date >= '2023-01-01' 
    AND sale_date < '2024-01-01'  -- Mejor para índices
),
customer_products AS (
  SELECT fs.customer_id, fs.product_id,
         SUM(fs.amount) as total_sales,
         AVG(fs.amount) as avg_sales,
         COUNT(*) as transaction_count
  FROM filtered_sales fs
  GROUP BY fs.customer_id, fs.product_id
  HAVING SUM(fs.amount) > 10000
)
SELECT c.customer_name, p.product_name,
       cp.total_sales, cp.avg_sales, cp.transaction_count
FROM customer_products cp
JOIN customers c ON cp.customer_id = c.customer_id
JOIN products p ON cp.product_id = p.product_id
WHERE c.customer_segment IN ('Premium', 'Gold')
  AND p.category = 'Electronics'
ORDER BY cp.total_sales DESC;
```

**B. Técnicas de Optimización:**

**1. Filtrado temprano:**

- Aplicar WHERE conditions antes de JOINs
- Usar partición de fechas si está disponible

**2. Indexing strategy:**

```sql
-- Índices recomendados
CREATE INDEX idx_sales_date_customer ON sales (sale_date, customer_id);
CREATE INDEX idx_customers_segment ON customers (customer_segment);
CREATE INDEX idx_products_category ON products (category);
```

**3. Reescritura de subqueries:**

```sql
-- EVITAR: Subquery correlacionada
SELECT * FROM customers c
WHERE EXISTS (
  SELECT 1 FROM sales s 
  WHERE s.customer_id = c.customer_id 
    AND s.amount > 1000
);

-- MEJOR: JOIN
SELECT DISTINCT c.*
FROM customers c
INNER JOIN sales s ON c.customer_id = s.customer_id
WHERE s.amount > 1000;
```

**3. ESTRATEGIAS ARQUITECTURALES:**

**A. Dividir la Query:**

```python
# Dividir en pasos más pequeños
def process_large_query():
    # Paso 1: Filtrar datos base
    filtered_data = """
    CREATE TEMP TABLE temp_sales AS
    SELECT * FROM sales 
    WHERE sale_date BETWEEN '2023-01-01' AND '2023-12-31'
    """
    
    # Paso 2: Agregar datos
    aggregated_data = """
    CREATE TEMP TABLE temp_aggregates AS
    SELECT customer_id, product_id, SUM(amount) as total
    FROM temp_sales
    GROUP BY customer_id, product_id
    """
    
    # Paso 3: Join final
    final_result = """
    SELECT c.customer_name, p.product_name, ta.total
    FROM temp_aggregates ta
    JOIN customers c ON ta.customer_id = c.customer_id
    JOIN products p ON ta.product_id = p.product_id
    """
```

**B. Materialized Views:**

```sql
-- Para queries que se ejecutan frecuentemente
CREATE MATERIALIZED VIEW mv_customer_sales_summary AS
SELECT customer_id, product_id, 
       DATE_TRUNC('month', sale_date) as month,
       SUM(amount) as monthly_sales,
       COUNT(*) as transaction_count
FROM sales
GROUP BY customer_id, product_id, DATE_TRUNC('month', sale_date);

-- Refresh periódico
REFRESH MATERIALIZED VIEW mv_customer_sales_summary;
```

**C. Partitioning:**

```sql
-- Si la tabla es muy grande, particionar
CREATE TABLE sales_partitioned (
    sale_id SERIAL,
    customer_id INT,
    sale_date DATE,
    amount DECIMAL
) PARTITION BY RANGE (sale_date);

CREATE TABLE sales_2023 PARTITION OF sales_partitioned
FOR VALUES FROM ('2023-01-01') TO ('2024-01-01');
```

**4. ALTERNATIVAS TECNOLÓGICAS:**

**A. Columnar Storage:**

- Convertir a Parquet para analytics
- Usar columnar databases (ClickHouse, BigQuery)

**B. Distributed Processing:**

```python
# Spark para queries muy grandes
from pyspark.sql import SparkSession

spark = SparkSession.builder.appName("LargeQuery").getOrCreate()

# Paralelizar la query
result = spark.sql("""
    SELECT customer_id, SUM(amount) as total
    FROM sales_table
    WHERE sale_date BETWEEN '2023-01-01' AND '2023-12-31'
    GROUP BY customer_id
""").repartition(200)  # Distribuir en múltiples partitions
```

**C. Caching Strategy:**

```sql
-- Cache resultados intermedios
CREATE TABLE cache_monthly_sales AS
SELECT customer_id, product_id, 
       DATE_TRUNC('month', sale_date) as month,
       SUM(amount) as total
FROM sales
GROUP BY customer_id, product_id, DATE_TRUNC('month', sale_date);

-- Query final usa cache
SELECT c.customer_name, cms.total
FROM cache_monthly_sales cms
JOIN customers c ON cms.customer_id = c.customer_id
WHERE cms.month = '2023-12-01';
```

**5. MONITOREO Y VALIDACIÓN:**

```python
def validate_optimization():
    # Comparar performance
    original_time = measure_query_time(original_query)
    optimized_time = measure_query_time(optimized_query)
    
    # Validar resultados
    assert compare_results(original_result, optimized_result)
    
    # Monitorear métricas
    metrics = {
        'execution_time': optimized_time,
        'rows_processed': count_rows_processed(),
        'memory_usage': get_memory_usage(),
        'improvement_factor': original_time / optimized_time
    }
    
    return metrics
```

**6. APPROACH SISTEMÁTICO:**

**Metodología paso a paso:**

1. **Profile primero:** Identificar bottlenecks reales
2. **Optimizar lo más impactante:** 80/20 rule
3. **Validar cada cambio:** No romper funcionalidad
4. **Documentar cambios:** Para futuro mantenimiento
5. **Monitorear producción:** Asegurar mejoras sostenibles

**Preguntas clave para hacer al entrevistador:**

- "¿Cuál es el volume de datos típico?"
- "¿Con qué frecuencia se ejecuta esta query?"
- "¿Hay constraints de tiempo real?"
- "¿Qué infraestructura tenemos disponible?"

**R:**

1. **Immediate assessment:**
    
    - Check monitoring dashboards
    - Review recent deployments
    - Examine error logs y stack traces
2. **Root cause analysis:**
    
    - Data lineage para entender dependencies
    - Compare con runs exitosos anteriores
    - Check data quality metrics
3. **Mitigation:**
    
    - Rollback si es deployment issue
    - Skip bad data si es data quality issue
    - Scale resources si es capacity issue
4. **Prevention:**
    
    - Improve monitoring y alerting
    - Add data validation checks
    - Implement circuit breakers

## 11. CLOUD Y ESCALABILIDAD

### P: ¿Cuáles son los principios fundamentales de sistemas distribuidos?

**R:**

**1. CAP Theorem:**

- **Consistency:** Todos los nodos ven los mismos datos al mismo tiempo
- **Availability:** Sistema sigue operativo incluso con fallos
- **Partition Tolerance:** Sistema continúa funcionando con fallos de red
- **Regla:** Solo puedes garantizar 2 de 3 simultáneamente

**Ejemplos:**

- **CP (Consistency + Partition Tolerance):** MongoDB, HBase
- **AP (Availability + Partition Tolerance):** Cassandra, DynamoDB
- **CA (Consistency + Availability):** Sistemas tradicionales RDBMS

**2. BASE vs ACID:**

**ACID (Transaccional):**

- **Atomicity:** Todo o nada
- **Consistency:** Estados válidos
- **Isolation:** Transacciones independientes
- **Durability:** Cambios permanentes

**BASE (Distribuido):**

- **Basically Available:** Sistema generalmente disponible
- **Soft State:** Estado puede cambiar sin input
- **Eventually Consistent:** Consistencia eventualmente

### P: ¿Qué es el teorema de consensus y cómo se implementa?

**R:**

**Consensus Problem:** Cómo hacer que múltiples nodos distribuidos acuerden sobre un valor.

**Algoritmos principales:**

**1. Raft:**

- Más simple de entender que Paxos
- Roles: Leader, Follower, Candidate
- Usado en: etcd, Consul, TikV

**Funcionamiento:**

```
1. Leader Election: Nodos eligen un líder
2. Log Replication: Líder replica logs a followers
3. Safety: Solo committed entries pueden ser aplicadas
```

**2. Paxos:**

- Más complejo pero más general
- Usado en: Google Spanner, Apache Zookeeper

**3. Byzantine Fault Tolerance:**

- Maneja nodos maliciosos
- Usado en: Blockchain, sistemas críticos

### P: ¿Cómo funciona la replicación en sistemas distribuidos?

**R:**

**Tipos de Replicación:**

**1. Master-Slave (Primary-Replica):**

```
Master (Write) → Slave1 (Read)
              → Slave2 (Read)
              → Slave3 (Read)
```

- **Ventajas:** Simple, consistencia fuerte
- **Desventajas:** Single point of failure, no distribuye writes
- **Uso:** MySQL replication, PostgreSQL streaming

**2. Master-Master (Multi-Master):**

```
Master1 ←→ Master2 ←→ Master3
```

- **Ventajas:** Alta disponibilidad, distribuye writes
- **Desventajas:** Conflictos de escritura, complejidad
- **Uso:** CouchDB, Riak

**3. Leaderless Replication:**

```
Client → Node1, Node2, Node3 (Quorum)
```

- **Ventajas:** No single point of failure
- **Desventajas:** Eventual consistency
- **Uso:** Cassandra, DynamoDB

**Estrategias de Consistencia:**

- **Strong Consistency:** Read-after-write garantizado
- **Eventual Consistency:** Datos convergen eventualmente
- **Causal Consistency:** Preserva relaciones causa-efecto

### P: ¿Qué es sharding y cómo lo implementarías?

**R:**

**Sharding** es dividir datos horizontalmente across múltiples nodos.

**Estrategias de Sharding:**

**1. Range-based Sharding:**

```python
def get_shard(user_id):
    if user_id < 1000000:
        return "shard1"
    elif user_id < 2000000:
        return "shard2"
    else:
        return "shard3"
```

- **Ventajas:** Simple, queries por rango eficientes
- **Desventajas:** Hot spots, balanceo desigual

**2. Hash-based Sharding:**

```python
def get_shard(user_id):
    return f"shard{hash(user_id) % num_shards}"
```

- **Ventajas:** Distribución uniforme
- **Desventajas:** No eficiente para range queries

**3. Directory-based Sharding:**

```python
# Lookup service mantiene mapeo
shard_directory = {
    "user123": "shard1",
    "user456": "shard2",
    "user789": "shard3"
}
```

- **Ventajas:** Flexibilidad completa
- **Desventajas:** Lookup overhead, single point of failure

**Consistent Hashing:**

```python
import hashlib

class ConsistentHash:
    def __init__(self, nodes=None, replicas=3):
        self.replicas = replicas
        self.ring = {}
        self.sorted_keys = []
        
        if nodes:
            for node in nodes:
                self.add_node(node)
    
    def add_node(self, node):
        for i in range(self.replicas):
            key = self.hash(f"{node}:{i}")
            self.ring[key] = node
            self.sorted_keys.append(key)
        self.sorted_keys.sort()
    
    def get_node(self, key):
        if not self.ring:
            return None
        
        hash_key = self.hash(key)
        idx = self.get_position(hash_key)
        return self.ring[self.sorted_keys[idx]]
    
    def hash(self, key):
        return int(hashlib.md5(key.encode()).hexdigest(), 16)
```

### P: ¿Cómo manejas la tolerancia a fallos en sistemas distribuidos?

**R:**

**Tipos de Fallos:**

1. **Crash Failures:** Nodo se detiene completamente
2. **Omission Failures:** Nodo no responde a algunos requests
3. **Byzantine Failures:** Nodo se comporta arbitrariamente
4. **Network Partitions:** Comunicación entre nodos falla

**Estrategias de Tolerancia:**

**1. Replication:**

```python
# Replicar datos en N nodos
def write_with_replication(key, value, replication_factor=3):
    nodes = select_nodes(key, replication_factor)
    successful_writes = 0
    
    for node in nodes:
        try:
            node.write(key, value)
            successful_writes += 1
        except Exception as e:
            log_error(f"Failed to write to {node}: {e}")
    
    # Quorum write: mayoría debe suceder
    if successful_writes >= (replication_factor // 2) + 1:
        return True
    else:
        raise Exception("Insufficient replicas written")
```

**2. Circuit Breaker Pattern:**

```python
class CircuitBreaker:
    def __init__(self, failure_threshold=5, timeout=60):
        self.failure_threshold = failure_threshold
        self.timeout = timeout
        self.failure_count = 0
        self.last_failure_time = None
        self.state = "CLOSED"  # CLOSED, OPEN, HALF_OPEN
    
    def call(self, func, *args, **kwargs):
        if self.state == "OPEN":
            if time.time() - self.last_failure_time > self.timeout:
                self.state = "HALF_OPEN"
            else:
                raise Exception("Circuit breaker is OPEN")
        
        try:
            result = func(*args, **kwargs)
            self.reset()
            return result
        except Exception as e:
            self.record_failure()
            raise e
    
    def record_failure(self):
        self.failure_count += 1
        self.last_failure_time = time.time()
        if self.failure_count >= self.failure_threshold:
            self.state = "OPEN"
```

**3. Retry with Exponential Backoff:**

```python
import time
import random

def retry_with_backoff(func, max_retries=3, base_delay=1):
    for attempt in range(max_retries):
        try:
            return func()
        except Exception as e:
            if attempt == max_retries - 1:
                raise e
            
            # Exponential backoff with jitter
            delay = base_delay * (2 ** attempt) + random.uniform(0, 1)
            time.sleep(delay)
```

### P: ¿Cómo diseñarías un sistema de mensajería distribuido como Kafka?

**R:**

**Componentes principales:**

**1. Brokers:**

- Servidores que almacenan mensajes
- Forman un cluster distribuido
- Cada broker maneja múltiples partitions

**2. Topics y Partitions:**

```
Topic: user-events
├── Partition 0: [msg1, msg2, msg3, ...]
├── Partition 1: [msg4, msg5, msg6, ...]
└── Partition 2: [msg7, msg8, msg9, ...]
```

**3. Replication:**

```python
# Cada partition tiene múltiples replicas
class Partition:
    def __init__(self, topic, partition_id, replicas):
        self.topic = topic
        self.partition_id = partition_id
        self.leader = replicas[0]
        self.followers = replicas[1:]
        self.in_sync_replicas = set(replicas)
    
    def produce(self, message):
        # Write to leader first
        self.leader.append(message)
        
        # Replicate to followers
        for follower in self.followers:
            try:
                follower.append(message)
            except Exception:
                self.in_sync_replicas.discard(follower)
```

**4. Consumer Groups:**

```python
class ConsumerGroup:
    def __init__(self, group_id, topic):
        self.group_id = group_id
        self.topic = topic
        self.consumers = []
        self.partition_assignment = {}
    
    def rebalance(self):
        # Redistribute partitions among consumers
        partitions = self.topic.get_partitions()
        consumers = self.get_active_consumers()
        
        # Round-robin assignment
        for i, partition in enumerate(partitions):
            consumer = consumers[i % len(consumers)]
            self.partition_assignment[partition] = consumer
```

**5. Guarantees:**

- **At-least-once:** Mensaje se entrega al menos una vez
- **At-most-once:** Mensaje se entrega máximo una vez
- **Exactly-once:** Mensaje se entrega exactamente una vez (más complejo)

### P: ¿Cómo implementarías distributed locks?

**R:**

**Problemas que resuelven:**

- Evitar que múltiples procesos modifiquen el mismo recurso
- Coordinación entre servicios distribuidos
- Leader election

**Implementaciones:**

**1. Database-based Lock:**

```sql
-- Tabla de locks
CREATE TABLE distributed_locks (
    resource_name VARCHAR(255) PRIMARY KEY,
    locked_by VARCHAR(255),
    locked_at TIMESTAMP,
    expires_at TIMESTAMP
);

-- Acquire lock
INSERT INTO distributed_locks (resource_name, locked_by, locked_at, expires_at)
VALUES ('resource1', 'service-instance-1', NOW(), NOW() + INTERVAL '30 seconds')
ON CONFLICT (resource_name) DO NOTHING;

-- Release lock
DELETE FROM distributed_locks 
WHERE resource_name = 'resource1' AND locked_by = 'service-instance-1';
```

**2. Redis-based Lock:**

```python
import redis
import uuid
import time

class RedisDistributedLock:
    def __init__(self, redis_client, key, timeout=30):
        self.redis = redis_client
        self.key = f"lock:{key}"
        self.timeout = timeout
        self.identifier = str(uuid.uuid4())
    
    def acquire(self):
        # SET if not exists with expiration
        result = self.redis.set(
            self.key, 
            self.identifier, 
            nx=True,  # Only set if not exists
            ex=self.timeout  # Expiration in seconds
        )
        return result is True
    
    def release(self):
        # Lua script for atomic check-and-delete
        lua_script = """
        if redis.call("get", KEYS[1]) == ARGV[1] then
            return redis.call("del", KEYS[1])
        else
            return 0
        end
        """
        result = self.redis.eval(lua_script, 1, self.key, self.identifier)
        return result == 1
```

**3. ZooKeeper-based Lock:**

```python
from kazoo.client import KazooClient

class ZooKeeperDistributedLock:
    def __init__(self, zk_client, path):
        self.zk = zk_client
        self.path = path
        self.node_path = None
    
    def acquire(self):
        # Create sequential ephemeral node
        self.node_path = self.zk.create(
            f"{self.path}/lock-",
            ephemeral=True,
            sequence=True
        )
        
        while True:
            children = self.zk.get_children(self.path)
            children.sort()
            
            # Check if we have the smallest sequence number
            if self.node_path.split('/')[-1] == children[0]:
                return True
            
            # Watch the node before us
            predecessor = None
            for child in children:
                if child < self.node_path.split('/')[-1]:
                    predecessor = child
            
            if predecessor:
                self.zk.exists(f"{self.path}/{predecessor}", watch=True)
                # Wait for predecessor to be deleted
                time.sleep(0.1)
```

### P: ¿Cómo manejas la observabilidad en sistemas distribuidos?

**R:**

**Tres pilares de observabilidad:**

**1. Logging:**

```python
import logging
import json
import traceback

class DistributedLogger:
    def __init__(self, service_name):
        self.service_name = service_name
        self.logger = logging.getLogger(service_name)
    
    def log_request(self, trace_id, request_id, method, url, user_id=None):
        log_entry = {
            "timestamp": time.time(),
            "service": self.service_name,
            "trace_id": trace_id,
            "request_id": request_id,
            "method": method,
            "url": url,
            "user_id": user_id,
            "type": "request"
        }
        self.logger.info(json.dumps(log_entry))
```

**2. Metrics:**

```python
from prometheus_client import Counter, Histogram, Gauge

# Contadores
REQUEST_COUNT = Counter('requests_total', 'Total requests', ['method', 'endpoint'])
REQUEST_DURATION = Histogram('request_duration_seconds', 'Request duration')
ACTIVE_CONNECTIONS = Gauge('active_connections', 'Active connections')

def track_request(method, endpoint):
    REQUEST_COUNT.labels(method=method, endpoint=endpoint).inc()
    
    start_time = time.time()
    try:
        # Process request
        pass
    finally:
        REQUEST_DURATION.observe(time.time() - start_time)
```

**3. Tracing:**

```python
from opentelemetry import trace
from opentelemetry.propagate import extract, inject

tracer = trace.get_tracer(__name__)

def process_request(request):
    # Extract trace context from incoming request
    context = extract(request.headers)
    
    with tracer.start_as_current_span("process_request", context=context) as span:
        span.set_attribute("user.id", request.user_id)
        span.set_attribute("request.method", request.method)
        
        # Call downstream service
        call_downstream_service(request)

def call_downstream_service(request):
    with tracer.start_as_current_span("call_downstream") as span:
        headers = {}
        inject(headers)  # Inject trace context
        
        response = requests.post(
            "http://downstream-service/api", 
            json=request.data,
            headers=headers
        )
        
        span.set_attribute("http.status_code", response.status_code)
```

### P: ¿Cómo migrarías un pipeline on-premise a la nube?

**R:**

1. **Assessment:**
    
    - Map dependencies y data flows
    - Evaluate data volumes y patterns
    - Identify security/compliance requirements
2. **Strategy:**
    
    - **Lift and shift:** Migrate as-is primero
    - **Re-architecting:** Optimize for cloud-native
    - **Hybrid approach:** Gradual migration
3. **Implementation:**
    
    - Start con non-critical workloads
    - Parallel run para validation
    - Monitor performance y costos
4. **Cloud services mapping:**
    
    - Compute: EC2/Compute Engine → Serverless functions
    - Storage: Local storage → S3/Cloud Storage
    - Databases: On-prem DB → Managed services

### P: ¿Cómo optimizarías costos en un pipeline cloud?

**R:**

1. **Compute optimization:**
    
    - Spot instances para workloads batch
    - Auto-scaling basado en demanda
    - Serverless para cargas variables
2. **Storage optimization:**
    
    - Lifecycle policies (S3 IA, Glacier)
    - Data compression y efficient formats
    - Delete old/unused data
3. **Monitoring:**
    
    - Cost tracking por proyecto/team
    - Alerts para spending anomalies
    - Regular cost reviews
4. **Architecture:**
    
    - Cache frequently accessed data
    - Optimize data partitioning
    - Use managed services where cost-effective