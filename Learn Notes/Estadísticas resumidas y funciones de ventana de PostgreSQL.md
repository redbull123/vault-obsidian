
# Windows Functions
## ROW_NUMBER()

```
ROW_NUMBER() OVER() AS Row_N
```

## ORDER BY
```
ROW_NUMBER() OVER (ORDER BY Year DESC, Event ASC) AS Row_N
```

## LAG

```
LAG(COLUMN, N) OVER() --> DEVUELVE EL VALOR DE COLUMN EN LA FILE N ANTES DE LA FILA ACTUAL

LAG(Champion, 1) OVER (ORDER BY YEAR ASC) AS Last_Champion
```

## PARTITION BY

- **PARTITION BY** splits the table into partitions based on a column's unique values.
- the results aren't rolled into one column.
- Operated on separately by the window function
- **ROW_NUMBER** will reset for each partition
- **LAG** will only fetch a row's previous value if its previous row is in the same partition.

```
WITH Discus_Gold AS (...)

SELECT
	Year, Event, Champion,
	LAG(Champion) OVER 
		(PARTITION BY Event ORDER BY Event ASC, Year ASC) AS Last_Champion
	FROM Discus_Gold
	ORDER BY Event ASC, Year ASC;
```

## Fetching

**Relative**
- **LAG**
- **LEAD(column, n)** returns column's value at the row **n** rows after the current row.
**Absolute**
- **FIRST_VALUE(column)** returns the first value in the table or partition.
- **LAST_VALUE(column)** returns the last value in the table or partition.

## Clasificacion

- ROW_NUMBER() : asigna un numero unico.
- RANK() : Asigna el mismo numero a las filas con valores identicos y se salta los siguientes numeros en esos casos.
- DENSE_RANK() : tambien asigna el mismo numero a las filas con valores identicos, pero no omite los numeros siguientes.

## Paging

- NTILE(n) -> divide los datos en aproximadamente n paginas iguales.

```
WITH Disciplines AS (
	SELECT 
		DISTINCT Discipline
		FROM Summer_Medals)
SELECT
	Discipline, NTILE(15) OVER() AS Page
FROM Disciplines
ORDER BY Page ASC;

```


## Aggregate Window Functions

- MAX()
- SUM()
- MIN()
## FRAMES

ROWS BETWEEN
- **ROWS BETWEEN [START] AND [FINISH]**
	- **n PRECEDING** : n filas antes de la fila actual
	- **CURRENT ROW** : la fila actual
	- **n FOLLOWING** : n filas despues de la fila actual
- Ejemplos
	- **ROWS BETWEEN 3 PRECEDING AND CURRENT ROW** ->Empieza 3 filas antes de la fila actual, y termina en la fila actual
	- **ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING** -> Empieza 1 fila antes de la fila actual, y 1 fila despues de la fila actual
	- **ROWS BETWEEN 5 PRECEDING AND 1 PRECEDING** -> Empieza 5 filas  antes de la fila actual y termina 1 fila antes de la fila actual 

Va dentro de OVER( AFTER DESC...)

## Medias Moviles y Totales

- Media Movil (MA): Media de los ultimos n periodos. Se utiliza para indicar impulso/tendencia. Tambien es util para eliminar la estacionalidad.
	Example:
```
WITH US_Medals AS (...)

SELECT
	Year, Medals,
	AVG(Medals) OVER
	(ORDER BY Year ASC
	ROWS BETWEEN
	2 PRECEDING AND CURRENT ROW) AS Medals_MA
FROM US_Medals
ORDER BY Year ASC;
```
- Total Movil: suma de los ultimos n periodos
```
WITH US_Medals AS (...)

SELECT
	Year, Medals,
	SUM(Medals) OVER
	(ORDER BY Year ASC
	ROWS BETWEEN
	2 PRECEDING AND CURRENT ROW) AS Medals_MT
FROM US_Medals
ORDER BY Year ASC;
```

- ROWS frente a RANGE
	- RANGE BETWEEN [START] AND [FINISH]
		- Funciona practicamente igual que ROWS BETWEEN
		- RANGE trata los duplicados en la subclausula ORDER BY de OVER como una sola entidad.
		- ROWS es preferida sobre RANGE

## Pivotar / Dinamizacion

- **CROSSTAB**
```
CREATE EXTENSION IF NOT EXITS tablefunc;

SELECT * FROM CROSSTAB($$
	source_sql TEXT
$$) AS ct (column_1 DATATYPE_1,
			column_2 DATATYPE_2,
			....
			column_n DATATYPE_N)
```

## Rollup y Cube

- RollUp es una subclausula GROUP BY que incluye mas filas para agregaciones a nivel de grupo.
- RollUp es jerarquico y se desagrega desde la columna mas a la izquierda hacia la mas a la derecha.
	- ROLLUP(Country, Medal) incluye los totales del nivel de Country.
	- ROLLUP(Medal, Country) incluye los totales del nivel de Medal.
	- Ambos incluyen los totales generales.
- Los totales a nivel de grupo contienen nulls; la fila con todos los null es el total general.

- CUBE es un sistema no jerarquico 
- Genera todas las agregaciones posibles a nivel de grupo.
	- CUBE(Country, Medal) cuenta el nivel Country, el nivel Medal y los totales generales.

## COALESCE
- COALESCE() toma una lista de valores y devuelve el primer valor no null, de izquierda a derecha.
- COALESCE(null,null,1,null,2) -> 1
- util cuando se utilizan operaciones SQL que devuelven null.
- Sintaxis: COALESCE(column, value_to_insert_where_there_a_null) ->

## STRING_AGG
- STRING_AGG(column, separator) toma todos los valores de una columna y los concatena, con separator entre cada valor.
EXAMPLE: STRING_AGG(Letter, ',') ->In select

```
WITH Country_Medals AS (...),

	Country_Ranks AS (...)
	
	SELECT STRING_AGG(Country, ', ')
	FROM Country_Medals;
```