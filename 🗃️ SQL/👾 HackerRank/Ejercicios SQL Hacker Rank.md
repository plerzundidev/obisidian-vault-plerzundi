
Query the list of _CITY_ names starting with vowels (i.e., `a`, `e`, `i`, `o`, or `u`) from **STATION**. Your result _cannot_ contain duplicates.

**Input Format**

The **STATION** table is described as follows:

![[1449345840-5f0a551030-Station 2.jpg]]

### Respuesta:

```sql
SELECT CITY
FROM STATION
WHERE LOWER(LEFT(CITY,1)) IN ('a','e','i','o','u')
GROUP BY CITY
```

**Explicación:**

- `LEFT(CITY, 1)`: Obtiene el primer carácter de la columna `CITY`.
- `LOWER(...)`: Convierte el carácter a minúscula para estandarizar la comparación.
- `IN ('a', 'e', 'i', 'o', 'u')`: Verifica si el primer carácter es una vocal.
- `DISTINCT`: Garantiza que los resultados sean únicos.


Query the list of _CITY_ names from **STATION** which have vowels (i.e., _a_, _e_, _i_, _o_, and _u_) as both their first _and_ last characters. Your result cannot contain duplicates.


**Input Format**

The **STATION** table is described as follows:

![[1449345840-5f0a551030-Station 2.jpg]]


```sql
SELECT CITY
FROM STATION
WHERE CITY LIKE '[aeiou]%' -- Empieza con vocal 
AND CITY LIKE '%[aeiou]' -- Termina con vocal
GROUP BY CITY
```

 **Explicación de la Optimización**

1. **Uso de `LIKE` con patrones**:
    
    - `[aeiou]%`: Filtra ciudades que **empiezan** con una vocal (`a`, `e`, `i`, `o`, `u`), sin importar mayúsculas/minúsculas (si el collation es `CI`).
        
    - `%[aeiou]`: Filtra ciudades que **terminan** con una vocal.
        
2. **Eliminación de funciones**:
    
    - Al evitar `LOWER`, `LEFT`, o `RIGHT`, se reduce el procesamiento de cada fila, mejorando la eficiencia.
        
3. **Ventaja adicional**:
    
    - SQL Server puede utilizar índices en la columna `CITY` (si existen) al usar `LIKE` con patrones que no comiencen con `%`.



Query the list of _CITY_ names from **STATION** that _do not start_ with vowels. Your result cannot contain duplicates.

**Input Format**

The **STATION** table is described as follows:

![[1449345840-5f0a551030-Station 2.jpg]]


```sql
SELECT CITY
FROM STATION
WHERE CITY NOT LIKE '[aeiou]%'
GROUP BY CITY
```


Query the list of _CITY_ names from **STATION** that _do not end_ with vowels. Your result cannot contain duplicates.

**Input Format**

The **STATION** table is described as follows:


![[1449345840-5f0a551030-Station 2.jpg]]


```sql
SELECT CITY
FROM STATION
WHERE CITY NOT LIKE '%[aeiou]' -- termina
GROUP BY CITY
```



Query the list of _CITY_ names from **STATION** that either do not start with vowels or do not end with vowels. Your result cannot contain duplicates.

**Input Format**

The **STATION** table is described as follows:

![[1449345840-5f0a551030-Station 2.jpg]]

```sql
SELECT CITY
FROM STATION
WHERE CITY NOT LIKE '[aeiou]%' OR CITY NOT LIKE '%[aeiou]'
GROUP BY CITY
```


Query the _Name_ of any student in **STUDENTS** who scored higher than _Marks_. Order your output by the _last three characters_ of each name. If two or more students both have names ending in the same last three characters (i.e.: Bobby, Robby, etc.), secondary sort them by ascending _ID_.

**Input Format**

The **STUDENTS** table is described as follows:

![[1443815243-94b941f556-1.png]]

The _Name_ column only contains uppercase (`A`-`Z`) and lowercase (`a`-`z`) letters.

**Sample Input**

![[1443815209-cf4b260993-2.png]]

**Sample Output**

```
Ashley
Julia
Belvet
```


```sql
SELECT Name
FROM STUDENTS
WHERE Marks > 75
ORDER BY
    RIGHT(name,3), -- Ordena por las últimas 3 letras del nombre
    ID ASC; -- Orden secundario por ID ascendente
```


Write a query that prints a list of employee names (i.e.: the _name_ attribute) for employees in **Employee** having a salary greater than $2000 per month who have been employees for less than 10 months. Sort your result by ascending _employee_id_.


**Input Format**

The **Employee** table containing employee data for a company is described as follows:

![[1458557872-4396838885-ScreenShot2016-03-21at4.27.13PM.png]]

where _employee_id_ is an employee's ID number, _name_ is their name, _months_ is the total number of months they've been working for the company, and _salary_ is the their monthly salary.

**Sample Input**

![[1458558612-af3da3ceb7-ScreenShot2016-03-21at4.32.59PM.png]]


```sql
select
    name
from employee
where salary > 2000 AND months < 10
order by
employee_id ASC;
```


Write a query identifying the _type_ of each record in the **TRIANGLES** table using its three side lengths. Output one of the following statements for each record in the table:

**Equilateral**: It's a triangle with 3 sides of equal length.
**Isosceles**: It's a triangle with 3 sides of equal length.
**Scalene**: It's a triangle with 3 sides of differing lengths.
**Not A Triangle**: The given values of _A_, _B_, and _C_ don't form a triangle.

**Input Format**

The **TRIANGLES** table is described as follows:

![[1443815629-ac2a843fb7-1.png]]

Each row in the table denotes the lengths of each of a triangle's three sides.

**Sample Input**

![[1443815827-cbfc1ca12b-2.png]]


```sql
SELECT 
    CASE 
        WHEN (A + B <= C) OR (A + C <= B) OR (B + C <= A) THEN 'Not A Triangle'
        WHEN A = B AND B = C THEN 'Equilateral'
        WHEN A = B OR A = C OR B = C THEN 'Isosceles'
        ELSE 'Scalene'
    END AS TriangleType
FROM TRIANGLES;
```
