
## Operaciones Básicas

### Consultas SELECT

```sql
-- Selección básica: extrae columnas específicas de una tabla
SELECT columna1, columna2 FROM tabla;

-- Selección de todas las columnas: trae todos los campos de la tabla
-- Evitar en producción con tablas grandes por rendimiento
SELECT * FROM tabla;

-- Selección con alias: renombra columnas en el resultado para mayor claridad
SELECT columna1 AS alias1, columna2 AS alias2 FROM tabla;

-- Selección con DISTINCT: elimina duplicados del resultado
-- Útil para obtener valores únicos o realizar análisis de cardinalidad
SELECT DISTINCT columna FROM tabla;

-- Selección con LIMIT: restringe el número de filas devueltas
-- Esencial para pruebas con grandes conjuntos de datos
SELECT * FROM tabla LIMIT 10;

-- Selección con OFFSET: salta las primeras n filas
-- Útil para paginación de resultados
SELECT * FROM tabla LIMIT 10 OFFSET 20; -- Trae filas 21-30
```

### Filtros y Condiciones

```sql
-- Filtro básico con WHERE: restringe filas según condición
-- Base para cualquier consulta que requiera filtrado
SELECT * FROM tabla WHERE condicion;

-- Operadores de comparación: evalúan condiciones sobre los datos
SELECT * FROM tabla WHERE columna = valor;  -- Igualdad exacta
SELECT * FROM tabla WHERE columna > valor;  -- Mayor que
SELECT * FROM tabla WHERE columna >= valor; -- Mayor o igual que
SELECT * FROM tabla WHERE columna < valor;  -- Menor que
SELECT * FROM tabla WHERE columna <= valor; -- Menor o igual que
SELECT * FROM tabla WHERE columna <> valor; -- Diferente de (también puede ser !=)

-- Múltiples condiciones: combinan varios criterios de filtrado
SELECT * FROM tabla WHERE condicion1 AND condicion2; -- Ambas condiciones deben ser verdaderas
SELECT * FROM tabla WHERE condicion1 OR condicion2;  -- Al menos una condición debe ser verdadera
SELECT * FROM tabla WHERE NOT condicion;             -- Niega la condición (devuelve lo opuesto)

-- Condiciones de rango: filtran valores dentro de un intervalo
-- Más eficiente y legible que usar >= y <= por separado
SELECT * FROM tabla WHERE columna BETWEEN valor1 AND valor2; -- Inclusivo en ambos extremos

-- Condiciones con listas: comprueban si un valor está en un conjunto definido
-- Mejor rendimiento que múltiples condiciones OR
SELECT * FROM tabla WHERE columna IN (valor1, valor2, valor3); -- Coincide con cualquier valor de la lista
SELECT * FROM tabla WHERE columna NOT IN (valor1, valor2, valor3); -- No coincide con ningún valor

-- Condiciones de texto: buscan patrones en campos de texto
-- % = cualquier cantidad de caracteres, _ = un solo carácter
SELECT * FROM tabla WHERE columna LIKE 'patrón%'; -- Comienza con "patrón"
SELECT * FROM tabla WHERE columna LIKE '%patrón'; -- Termina con "patrón"
SELECT * FROM tabla WHERE columna LIKE '%patrón%'; -- Contiene "patrón" en cualquier posición

-- Condiciones con NULL: verifican valores ausentes
-- NULL representa ausencia de valor, no cero ni cadena vacía
SELECT * FROM tabla WHERE columna IS NULL; -- Encuentra registros sin valor
SELECT * FROM tabla WHERE columna IS NOT NULL; -- Encuentra registros con algún valor
```

## Agregación y Agrupación

### Funciones de Agregación

```sql
-- Conteo de registros: cuantifica filas en un conjunto de resultados
SELECT COUNT(*) FROM tabla; -- Cuenta todas las filas, incluidos duplicados y NULL
SELECT COUNT(columna) FROM tabla; -- Solo cuenta filas donde columna no es NULL

-- Funciones matemáticas agregadas: operaciones sobre conjuntos de valores
SELECT SUM(columna) FROM tabla; -- Suma todos los valores no NULL
SELECT AVG(columna) FROM tabla; -- Calcula el promedio, ignorando NULL
SELECT MIN(columna) FROM tabla; -- Encuentra el valor mínimo
SELECT MAX(columna) FROM tabla; -- Encuentra el valor máximo

-- Combinación de agregaciones: múltiples cálculos en una sola consulta
-- Facilita análisis complejos con menos código y mejor rendimiento
SELECT 
    COUNT(*) AS total_registros,
    AVG(columna1) AS promedio,
    SUM(columna2) AS suma_total,
    MAX(columna3) AS valor_maximo,
    MIN(columna3) AS valor_minimo
FROM tabla;
```

### Agrupamiento

```sql
-- GROUP BY básico: segmenta datos para análisis por categoría
-- Cada columna en SELECT debe estar en GROUP BY o ser una función de agregación
SELECT columna1, COUNT(*) FROM tabla GROUP BY columna1;

-- GROUP BY con múltiples columnas: agrupación jerárquica
-- Crea segmentos más específicos (como subcategorías)
SELECT columna1, columna2, SUM(columna3) 
FROM tabla 
GROUP BY columna1, columna2;

-- GROUP BY con HAVING: filtra grupos después de la agregación
-- Similar a WHERE pero se aplica después de formar los grupos
SELECT columna1, COUNT(*) 
FROM tabla 
GROUP BY columna1 
HAVING COUNT(*) > 5; -- Solo muestra grupos con más de 5 registros

-- GROUP BY con ORDER BY: ordena los resultados agrupados
-- Útil para identificar rápidamente los grupos más/menos significativos
SELECT columna1, COUNT(*) AS conteo 
FROM tabla 
GROUP BY columna1 
ORDER BY conteo DESC; -- Ordena de mayor a menor
```

## Joins y Relaciones

### Tipos de JOIN

```sql
-- INNER JOIN (intersección): solo registros con coincidencias en ambas tablas
-- Útil cuando solo interesan datos que existen en ambas tablas
SELECT t1.columna1, t2.columna2
FROM tabla1 t1
INNER JOIN tabla2 t2 ON t1.columna_comun = t2.columna_comun;

-- LEFT JOIN: todos los registros de la tabla izquierda + coincidencias de la derecha
-- Conserva todos los registros de la primera tabla, incluso sin coincidencias
SELECT t1.columna1, t2.columna2
FROM tabla1 t1
LEFT JOIN tabla2 t2 ON t1.columna_comun = t2.columna_comun;
-- Donde no hay coincidencia, t2.columna2 será NULL

-- RIGHT JOIN: todos los registros de la tabla derecha + coincidencias de la izquierda
-- Opuesto al LEFT JOIN, mantiene todos los registros de la segunda tabla
SELECT t1.columna1, t2.columna2
FROM tabla1 t1
RIGHT JOIN tabla2 t2 ON t1.columna_comun = t2.columna_comun;

-- FULL OUTER JOIN: todos los registros de ambas tablas
-- Combina LEFT y RIGHT JOIN, mostrando todos los datos independientemente de coincidencias
SELECT t1.columna1, t2.columna2
FROM tabla1 t1
FULL OUTER JOIN tabla2 t2 ON t1.columna_comun = t2.columna_comun;

-- CROSS JOIN: producto cartesiano (cada fila de t1 con cada fila de t2)
-- Produce todas las combinaciones posibles entre las tablas
-- ¡Cuidado! Genera n×m filas, donde n=filas de t1 y m=filas de t2
SELECT t1.columna1, t2.columna2
FROM tabla1 t1
CROSS JOIN tabla2 t2;

-- Self JOIN: una tabla se une a sí misma
-- Útil para relaciones jerárquicas (empleados-gerentes, categorías-subcategorías)
SELECT t1.nombre AS empleado, t2.nombre AS gerente
FROM empleados t1
JOIN empleados t2 ON t1.id_gerente = t2.id_empleado;
```

## Subconsultas

### Tipos de Subconsultas

```sql
-- Subconsulta en WHERE: filtra basado en el resultado de otra consulta
-- Útil para condiciones dinámicas o complejas
SELECT columna1
FROM tabla1
WHERE columna2 IN (
    SELECT columna2 
    FROM tabla2 
    WHERE condicion
);
-- Busca en tabla1 los registros cuya columna2 aparece en los resultados de la subconsulta

-- Subconsulta en FROM (tabla derivada): trata un resultado como tabla temporal
-- Permite realizar operaciones sobre resultados intermedios
SELECT t.columna1
FROM (
    SELECT * 
    FROM tabla 
    WHERE condicion
) AS t;
-- La subconsulta debe tener un alias (aquí "t")

-- Subconsulta en SELECT: calcula valores para cada fila del resultado principal
-- Puede impactar rendimiento en grandes conjuntos de datos
SELECT 
    columna1,
    (SELECT MAX(columna2) FROM tabla2 WHERE tabla2.id = tabla1.id) AS max_valor
FROM tabla1;

-- Subconsulta correlacionada: referencia a la consulta exterior
-- Ejecuta la subconsulta para cada fila de la consulta exterior
SELECT columna1
FROM tabla1 t1
WHERE columna2 > (
    SELECT AVG(columna2) 
    FROM tabla1 t2 
    WHERE t2.columna3 = t1.columna3
);
-- Encuentra registros donde columna2 es mayor que el promedio de su grupo (columna3)

-- Subconsulta con EXISTS: comprueba si existen registros que cumplan una condición
-- Más eficiente que IN para grandes volúmenes de datos
SELECT columna1
FROM tabla1
WHERE EXISTS (
    SELECT 1 
    FROM tabla2 
    WHERE tabla2.id = tabla1.id
);
-- Devuelve filas de tabla1 que tienen al menos una coincidencia en tabla2
```

## Manipulación de Datos

### Inserción de Datos

```sql
-- Inserción básica: agrega una sola fila con valores específicos
-- Las columnas no especificadas recibirán NULL o su valor DEFAULT
INSERT INTO tabla (columna1, columna2) VALUES (valor1, valor2);

-- Inserción múltiple: agrega varias filas en una sola operación
-- Más eficiente que múltiples INSERT individuales
INSERT INTO tabla (columna1, columna2)
VALUES 
    (valor1_1, valor1_2), 
    (valor2_1, valor2_2), 
    (valor3_1, valor3_2);

-- Inserción desde consulta: popula tabla con resultados de otra consulta
-- Ideal para migración de datos o generación de tablas derivadas
INSERT INTO tabla_destino (columna1, columna2)
SELECT columna1, columna2 
FROM tabla_origen 
WHERE condicion;
```

### Actualización de Datos

```sql
-- Actualización básica: modifica valores en filas que cumplen una condición
-- SIEMPRE incluir WHERE para evitar actualizaciones masivas no intencionadas
UPDATE tabla SET columna1 = valor1 WHERE condicion;

-- Actualización múltiple: modifica varias columnas simultáneamente
-- Más eficiente que múltiples UPDATE separados
UPDATE tabla 
SET 
    columna1 = valor1, 
    columna2 = valor2 
WHERE condicion;

-- Actualización con JOIN: modifica datos basados en valores de otra tabla
-- Útil para sincronización entre tablas relacionadas
UPDATE tabla1 t1
SET t1.columna = t2.columna
FROM tabla2 t2
WHERE t1.id = t2.id AND condicion;

-- En MySQL la sintaxis es distinta:
UPDATE tabla1 t1
JOIN tabla2 t2 ON t1.id = t2.id
SET t1.columna = t2.columna
WHERE condicion;
```

### Eliminación de Datos

```sql
-- Eliminación con filtro: borra filas específicas
-- SIEMPRE incluir WHERE para evitar eliminaciones masivas no intencionadas
DELETE FROM tabla WHERE condicion;

-- Eliminación de todos los registros: vacía completamente la tabla
DELETE FROM tabla; -- Mantiene estructura e índices, registra cada eliminación

-- Truncamiento: alternativa más rápida para vaciar tablas
TRUNCATE TABLE tabla; -- Reinicia la tabla, no registra eliminaciones individuales
-- No dispara triggers, reinicia secuencias y es más rápido que DELETE
```


### Explicación de la Cláusula `MERGE` en SQL

#### Propósito
Sincronizar una tabla objetivo (`targetTable`) con los datos de una tabla fuente (`sourceTable`) realizando operaciones de **INSERT, UPDATE y DELETE** en una sola sentencia.

---

##### Estructura del Código
```sql
MERGE INTO targetTable
USING sourceTable
ON (targetTable.PKID = sourceTable.PKID)
WHEN MATCHED AND (targetTable.PKID > 100) THEN
    DELETE
WHEN MATCHED AND (targetTable.PKID <= 100) THEN
    UPDATE SET
        targetTable.ColumnA = sourceTable.ColumnA,
        targetTable.ColumnB = sourceTable.ColumnB
WHEN NOT MATCHED THEN
    INSERT (ColumnA, ColumnB) 
    VALUES (sourceTable.ColumnA, sourceTable.ColumnB)
WHEN NOT MATCHED BY SOURCE THEN
    DELETE;
## Operaciones con Tablas

### Creación de Tablas

```sql
-- Creación básica: define una nueva tabla con sus columnas y restricciones
CREATE TABLE tabla (
    id INT PRIMARY KEY, -- Clave primaria, garantiza unicidad e indexa automáticamente
    nombre VARCHAR(50) NOT NULL, -- Restricción NOT NULL evita valores ausentes
    fecha DATE, -- Tipo fecha sin componente de hora
    valor DECIMAL(10,2), -- Número decimal con 10 dígitos totales, 2 decimales
    activo BOOLEAN DEFAULT TRUE -- Valor predeterminado si no se especifica
);

-- Creación con clave foránea: establece relación entre tablas
CREATE TABLE tabla (
    id INT PRIMARY KEY,
    id_relacion INT,
    nombre VARCHAR(50),
    FOREIGN KEY (id_relacion) REFERENCES otra_tabla(id)
    -- Garantiza integridad referencial: cada id_relacion debe existir en otra_tabla
);

-- Creación a partir de consulta: genera tabla y la popula en un solo paso
-- Útil para tablas derivadas o materializadas
CREATE TABLE tabla_nueva AS
SELECT * FROM tabla_existente WHERE condicion;
```



##### explicación Paso a Paso

###### 1. **`MERGE INTO ... USING ... ON`**

- **Tabla objetivo**: `targetTable` (se modificará).
- **Tabla fuente**: `sourceTable` (contiene los datos de referencia).
- **Condición de coincidencia**: `PKID` es la clave primaria para relacionar registros.
    
###### 2. **`WHEN MATCHED AND (PKID > 100) THEN DELETE`**

- Si un registro existe en ambas tablas y `PKID > 100`:
    
    - **Acción**: Elimina el registro de `targetTable`.
        

###### 3. **`WHEN MATCHED AND (PKID <= 100) THEN UPDATE`**

- Si un registro existe en ambas tablas y `PKID ≤ 100`:
    
    - **Acción**: Actualiza `ColumnA` y `ColumnB` en `targetTable` con los valores de `sourceTable`.
        

###### 4. **`WHEN NOT MATCHED THEN INSERT`**

- Si un registro existe en `sourceTable` pero no en `targetTable`:
    
    - **Acción**: Inserta un nuevo registro en `targetTable`.
        

###### 5. **`WHEN NOT MATCHED BY SOURCE THEN DELETE`**

- Si un registro existe en `targetTable` pero no en `sourceTable`:
    
    - **Acción**: Elimina el registro de `targetTable`.
### Alteración de Tablas

```sql
-- Agregar columna: extiende estructura existente
ALTER TABLE tabla ADD COLUMN nueva_columna INT;

-- Modificar columna: cambia tipo o atributos
ALTER TABLE tabla MODIFY COLUMN columna VARCHAR(100); -- SQL Standard
ALTER TABLE tabla ALTER COLUMN columna TYPE VARCHAR(100); -- PostgreSQL

-- Renombrar columna: cambia identificador manteniendo datos
ALTER TABLE tabla RENAME COLUMN nombre_antiguo TO nombre_nuevo;

-- Eliminar columna: quita campo y sus datos (irreversible)
ALTER TABLE tabla DROP COLUMN columna;

-- Agregar constraint: añade restricciones a datos existentes
ALTER TABLE tabla ADD CONSTRAINT nombre_constraint PRIMARY KEY (columna);
ALTER TABLE tabla ADD CONSTRAINT nombre_fk 
    FOREIGN KEY (columna) REFERENCES otra_tabla(columna);
```

### Eliminación de Tablas

```sql
-- Eliminar tabla: elimina estructura y todos los datos
DROP TABLE tabla; -- Falla si la tabla no existe

-- Eliminar si existe: versión segura que no produce errores
DROP TABLE IF EXISTS tabla; -- No falla si la tabla no existe
```


### COPY POSTGRESQL

```sql
-- IMPORTAR CSV desde la ruta montada
COPY tabla(campo1,campo2,campo3,n_campos)
FROM '/csv_data/datasets.csv'
DELIMITER ',' CSV HEADER;
```


## Funciones de Fecha y Tiempo

```sql
-- Obtener fecha/hora actual: funciones del sistema para timestamp actual
SELECT CURRENT_DATE; -- Solo fecha (año-mes-día)
SELECT CURRENT_TIME; -- Solo hora (hora:minuto:segundo)
SELECT CURRENT_TIMESTAMP; -- Fecha y hora completa

-- Manipulación de fechas (PostgreSQL): operaciones aritméticas con fechas
SELECT fecha + INTERVAL '1 day'; -- Suma un día
SELECT fecha + INTERVAL '2 months'; -- Suma dos meses
SELECT fecha + INTERVAL '1 year'; -- Suma un año

-- Extracción de componentes: obtiene partes específicas de fechas
SELECT EXTRACT(YEAR FROM fecha) AS año; -- Extrae solo el año
SELECT EXTRACT(MONTH FROM fecha) AS mes; -- Extrae solo el mes (1-12)
SELECT EXTRACT(DAY FROM fecha) AS dia; -- Extrae solo el día (1-31)

-- Diferencia entre fechas: cálculo de intervalos temporales
SELECT DATE_PART('day', fecha2 - fecha1) AS dias_diferencia; -- PostgreSQL
-- En MySQL: DATEDIFF(fecha2, fecha1)
```

## Funciones de Texto

```sql
-- Concatenación: combina campos o strings
SELECT CONCAT(columna1, ' ', columna2); -- Estándar SQL
SELECT columna1 || ' ' || columna2; -- PostgreSQL, SQLite

-- Mayúsculas y minúsculas: normalización de texto
SELECT UPPER(columna); -- Convierte todo a mayúsculas
SELECT LOWER(columna); -- Convierte todo a minúsculas

-- Substring: extracción de partes de texto
SELECT SUBSTRING(columna FROM 1 FOR 5); -- Estándar SQL, extrae primeros 5 caracteres
SELECT SUBSTR(columna, 1, 5); -- Sintaxis alternativa en algunos dialectos

-- Reemplazo: sustituye texto dentro de cadenas
SELECT REPLACE(columna, 'buscar', 'reemplazar');
-- Reemplaza todas las ocurrencias de 'buscar' con 'reemplazar'

-- Eliminar espacios: limpieza de datos
SELECT TRIM(columna); -- Elimina espacios al inicio y final
SELECT LTRIM(columna); -- Solo elimina espacios al inicio (izquierda)
SELECT RTRIM(columna); -- Solo elimina espacios al final (derecha)

-- Longitud: mide tamaño de cadenas
SELECT LENGTH(columna); -- Cuenta caracteres (o bytes, dependiendo del dialecto)
```

## Funciones de Ventana (Window Functions)

```sql
-- Numeración de filas: asigna números secuenciales
SELECT 
    columna,
    ROW_NUMBER() OVER (ORDER BY columna) AS num
FROM tabla;
-- Numera filas según el orden de 'columna', cada fila tiene número único

-- Numeración por partición: reinicia numeración por grupo
SELECT 
    columna1,
    columna2,
    ROW_NUMBER() OVER (PARTITION BY columna2 ORDER BY columna3) AS num
FROM tabla;
-- Numera filas dentro de cada grupo definido por columna2

-- Funciones de ranking: variantes de numeración con manejo de empates
SELECT 
    columna,
    RANK() OVER (ORDER BY columna) AS ranking, -- Deja huecos en numeración si hay empates
    DENSE_RANK() OVER (ORDER BY columna) AS ranking_denso, -- Sin huecos en numeración
    NTILE(4) OVER (ORDER BY columna) AS cuartil -- División en cuartiles (o n-tiles)
FROM tabla;

-- Funciones analíticas: cálculos sobre grupos sin agregar filas
SELECT 
    columna1, -- Dimensión o categoría
    columna2, -- Valor métrico
    SUM(columna2) OVER (PARTITION BY columna1) AS suma_grupo, -- Total por grupo
    AVG(columna2) OVER (PARTITION BY columna1) AS promedio_grupo, -- Promedio por grupo
    columna2 / SUM(columna2) OVER (PARTITION BY columna1) AS porcentaje -- % del total
FROM tabla;
-- Mantiene detalle de filas pero añade métricas agregadas de contexto

-- Acceso a filas relacionadas: valores de filas adyacentes
SELECT 
    columna1,
    columna2,
    LAG(columna2) OVER (ORDER BY columna1) AS valor_anterior, -- Valor de fila previa
    LEAD(columna2) OVER (ORDER BY columna1) AS valor_siguiente -- Valor de fila siguiente
FROM tabla;
-- Útil para cálculos de diferencias secuenciales

-- Acumulados: sumas progresivas o ventanas móviles
SELECT 
    columna1,
    columna2,
    SUM(columna2) OVER (ORDER BY columna1) AS suma_acumulada, -- Total acumulativo
    SUM(columna2) OVER (
        ORDER BY columna1 
        ROWS BETWEEN 2 PRECEDING AND CURRENT ROW
    ) AS suma_3_filas -- Ventana móvil de 3 filas (2 anteriores + actual)
FROM tabla;
```

## Gestión de Vistas

```sql
-- Crear vista: consulta almacenada como objeto de base de datos
CREATE VIEW nombre_vista AS
SELECT columna1, columna2
FROM tabla
WHERE condicion;
-- Las vistas no almacenan datos, ejecutan la consulta cada vez que se accede

-- Crear o reemplazar vista: actualiza definición existente
CREATE OR REPLACE VIEW nombre_vista AS
SELECT columna1, columna2
FROM tabla
WHERE condicion;
-- Si ya existe la vista, actualiza su definición; si no, la crea

-- Crear vista materializada (PostgreSQL): almacena resultados físicamente
CREATE MATERIALIZED VIEW nombre_vista AS
SELECT columna1, columna2
FROM tabla
WHERE condicion;
-- Almacena físicamente los resultados para acceso más rápido
-- No se actualiza automáticamente cuando cambian los datos subyacentes

-- Actualizar vista materializada: refresca datos almacenados
REFRESH MATERIALIZED VIEW nombre_vista;
-- Vuelve a ejecutar la consulta y actualiza los datos almacenados

-- Eliminar vista: elimina definición (no afecta tablas base)
DROP VIEW nombre_vista;
DROP MATERIALIZED VIEW nombre_vista;
```

## Optimización y Rendimiento

### Índices


### **¿Qué hacer si la fragmentación vuelve a subir?**

- **Fragmentación < 30%**: Usa `REORGANIZE`.
- **Fragmentación ≥ 30%**: Usa `REBUILD`.
- 

```sql
-- Crear índice básico: acelera búsquedas por columna específica
CREATE INDEX idx_nombre ON tabla(columna);
-- Mejora rendimiento en WHERE, JOIN y ORDER BY sobre columna indexada

-- Crear índice único: garantiza valores únicos además de indexar
CREATE UNIQUE INDEX idx_nombre ON tabla(columna);
-- Impide duplicados y mejora rendimiento de búsqueda

-- Crear índice compuesto: optimiza búsquedas por múltiples columnas
CREATE INDEX idx_nombre ON tabla(columna1, columna2);
-- Útil cuando se filtra o ordena por combinación de columnas
-- El orden de las columnas es crucial: primero las más discriminantes

-- Eliminar índice: quita índice sin afectar datos
DROP INDEX idx_nombre;
-- Puede mejorar rendimiento de escritura pero deteriora rendimiento de lectura

```

### Análisis de Consultas

```sql
-- Explicar plan de ejecución: muestra cómo se ejecutará la consulta
EXPLAIN SELECT * FROM tabla WHERE condicion;
-- Muestra operaciones (scan, join, sort) sin ejecutar la consulta

-- Explicar y analizar en PostgreSQL: muestra plan y tiempos reales
EXPLAIN ANALYZE SELECT * FROM tabla WHERE condicion;
-- Ejecuta la consulta y muestra tiempos reales de cada operación

-- Estadísticas de tabla (PostgreSQL): actualiza metadatos para optimizador
ANALYZE tabla;
-- Recolecta estadísticas que usa el optimizador para elegir planes eficientes
```

## Transacciones

```sql
-- Iniciar transacción: agrupa operaciones como unidad atómica
BEGIN;
-- o
START TRANSACTION;
-- Todas las operaciones posteriores forman parte de la transacción

-- Confirmar cambios: hace permanentes todas las operaciones de la transacción
COMMIT;
-- Finaliza la transacción y aplica todos los cambios

-- Deshacer cambios: revierte todas las operaciones de la transacción
ROLLBACK;
-- Cancela la transacción y revierte todos los cambios

-- Savepoints: puntos de guardado dentro de transacción
SAVEPOINT nombre_punto; -- Crea punto de guardado
ROLLBACK TO SAVEPOINT nombre_punto; -- Revierte hasta el punto guardado
RELEASE SAVEPOINT nombre_punto; -- Elimina punto de guardado
-- Permite granularidad en control de transacciones complejas
```

## Funciones Comunes para Big Data

```sql
-- Particionado (Hive, BigQuery, etc.): divide tablas grandes en segmentos
CREATE TABLE tabla_particionada (
    id INT,
    fecha DATE,
    valor DECIMAL(10,2)
)
PARTITIONED BY (año INT, mes INT);
-- Organiza datos en subdirectorios según valores de las columnas de partición
-- Mejora rendimiento filtrando particiones completas (pruning)

-- Inserción en tabla particionada: especifica partición destino
INSERT INTO tabla_particionada PARTITION(año=2023, mes=4)
SELECT id, fecha, valor FROM origen;
-- Inserta datos directamente en la partición correspondiente

-- Muestreo de datos: toma subconjunto aleatorio para análisis rápido
SELECT * FROM tabla TABLESAMPLE(10 PERCENT);
-- Muestrea aproximadamente 10% de las filas o bloques

-- Ventanas temporales (BigQuery): analiza datos recientes
SELECT 
    columna1,
    COUNT(*) 
FROM tabla
WHERE fecha BETWEEN TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 7 DAY) AND CURRENT_TIMESTAMP()
GROUP BY columna1;
-- Limitación a los últimos 7 días de datos
```

## Características Específicas por Motor

### PostgreSQL

```sql
-- Tipos JSON: manipulación de datos semiestructurados
SELECT columna->>'propiedad' AS valor FROM tabla; -- Extrae valor como texto
SELECT columna->'objeto'->>'propiedad' AS valor FROM tabla; -- Navega jerarquía
-- -> devuelve JSON, ->> devuelve texto

-- Arrays: trabajo con campos de tipo array
SELECT array_column[1] FROM tabla; -- Accede a elemento específico (indexado desde 1)
SELECT unnest(array_column) FROM tabla; -- Expande array a filas individuales

-- CTEs recursivas: consultas jerárquicas
WITH RECURSIVE cte AS (
    -- Consulta base (ancla)
    SELECT id, parent_id, nombre, 1 AS nivel FROM tabla WHERE parent_id IS NULL
    UNION ALL
    -- Parte recursiva
    SELECT t.id, t.parent_id, t.nombre, cte.nivel + 1
    FROM tabla t
    JOIN cte ON t.parent_id = cte.id
)
SELECT * FROM cte;
-- Útil para trabajar con datos jerárquicos (árboles, organigramas)
```

### MySQL

```sql
-- Reemplazar registro (upsert): inserta o actualiza en una operación
INSERT INTO tabla (id, columna) VALUES (1, 'valor')
ON DUPLICATE KEY UPDATE columna = 'valor';
-- Si existe registro con misma clave, actualiza; si no, inserta

-- Funciones de grupo_concat: agrega valores de texto
SELECT 
    otra_columna,
    GROUP_CONCAT(columna SEPARATOR ',') AS valores_concatenados 
FROM tabla 
GROUP BY otra_columna;
-- Agrega valores de columna en una lista separada por comas para cada grupo
```

### Snowflake

```sql
-- Manejo de variantes: acceso a datos semiestructurados
SELECT columna:propiedad::string AS valor FROM tabla;
-- Accede a propiedad dentro de JSON/objeto y convierte a string

-- Copiar datos desde S3: carga desde almacenamiento en nube
COPY INTO tabla
FROM 's3://bucket/ruta/'
FILE_FORMAT = (TYPE = 'CSV' FIELD_DELIMITER = ',' SKIP_HEADER = 1);
-- Carga masiva desde S3 especificando formato y opciones
```

### BigQuery

```sql
-- Particiones de tiempo: organiza datos por período
CREATE TABLE dataset.tabla
PARTITION BY DATE_TRUNC(fecha, DAY)
AS SELECT * FROM origen;
-- Divide automáticamente datos en particiones diarias según columna fecha

-- Arrays y estructuras: tipos de datos complejos
SELECT 
    otra_columna,
    ARRAY_AGG(columna) AS array_col 
FROM tabla 
GROUP BY otra_columna;
-- Agrega valores en un array para cada grupo

SELECT STRUCT(columna1 AS campo1, columna2 AS campo2) AS struct_col 
FROM tabla;
-- Crea estructura de datos con campos nombrados
```

## Ejemplos de Patrones Comunes para Data Engineers

### ETL Básico

```sql
-- 1. Extracción a tabla temporal
CREATE TEMPORARY TABLE temp_datos AS
SELECT * FROM origen WHERE fecha >= CURRENT_DATE - INTERVAL '7 days';

-- 2. Transformación (limpieza y enriquecimiento)
UPDATE temp_datos
SET 
    columna1 = TRIM(UPPER(columna1)),
    columna2 = CASE 
                WHEN columna2 IS NULL THEN 'Desconocido'
                ELSE columna2
               END;

-- 3. Carga a destino (inserción incremental)
INSERT INTO destino (columna1, columna2, fecha_carga)
SELECT 
    columna1, 
    columna2,
    CURRENT_TIMESTAMP
FROM temp_datos t
WHERE NOT EXISTS (
    SELECT 1 FROM destino d WHERE d.id = t.id
);
```

### Análisis de Tendencias

```sql
-- Cálculo de métricas con cambio porcentual
WITH metricas_mensuales AS (
    SELECT 
        DATE_TRUNC('month', fecha) AS mes,
        SUM(ventas) AS ventas_totales
    FROM tabla
    GROUP BY DATE_TRUNC('month', fecha)
),
metricas_con_previo AS (
    SELECT 
        mes,
        ventas_totales,
        LAG(ventas_totales) OVER (ORDER BY mes) AS ventas_mes_anterior
    FROM metricas_mensuales
)
SELECT 
    mes,
    ventas_totales,
    ventas_mes_anterior,
    ((ventas_totales - ventas_mes_anterior) / ventas_mes_anterior * 100)::DECIMAL(10,2) AS cambio_porcentual
FROM metricas_con_previo
WHERE ventas_mes_anterior IS NOT NULL;
```

### Detección de Duplicados

```sql
-- Identificación de registros duplicados
WITH duplicados AS (
    SELECT 
        columna1, 
        columna2,
        COUNT(*) AS num_ocurrencias
    FROM tabla
    GROUP BY columna1, columna2
    HAVING COUNT(*) > 1
)
SELECT t.*
FROM tabla t
JOIN duplicados d ON t.columna1 = d.columna1 AND t.columna2 = d.columna2
ORDER BY t.columna1, t.columna2;
```

### Control de Calidad de Datos

```sql
-- Validación de integridad y completitud
SELECT 
    COUNT(*) AS total_registros,
    SUM(CASE WHEN columna1 IS NULL THEN 1 ELSE 0 END) AS nulos_columna1,
    SUM(CASE WHEN columna2 IS NULL THEN 1 ELSE 0 END) AS nulos_columna2,
    SUM(CASE WHEN columna3 < 0 THEN 1 ELSE 0 END) AS negativos_columna3,
    SUM(CASE WHEN columna_fecha > CURRENT_DATE THEN 1 ELSE 0 END) AS fechas_futuro,
    SUM(CASE WHEN LENGTH(columna_texto) > 100 THEN 1 ELSE 0 END) AS textos_largos
FROM tabla;
```