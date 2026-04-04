
## Sentencias CASE
> Contine sentencias WHEN, THEN y ELSE, Y termina con END

	CASE WHEN x = 1 THEN 'a'
		 WHEN x = 2 THEN 'b'
		 ELSE 'c' END AS new_column
Example:

	SELECT
		id,
		home_goal,
		away_goal,
		CASE WHEN home_goal > away_goal THEN 'Home Team Win'
			  ELSE 'Tie' END AS outcome
		FROM amtch
		WHERE season = '2013/2014';

## Subquerys
It could be anyway of a query:
- SELECT, FROM, WHERE, GROUP BY
It can get you different info:
- Amount
- List
- Table

	SELECT home_goal,
	FROM match
	WHERE home_goal > (
		SELECT AVG(home_goal)
		FROM match);
	SELECT AVG(home_goal) FROM match;

## SubQuerys in FROM

	```
	SELECT team,home_avg
	FROM (SELECT
			t.team_long_name AS team,
			AVG(m.home_goal) AS home_avg
		FROM match AS m
		LEFT JOIN team AS T
		ON m.hometeam_id = t.team_api_id
		WHERE season = '2011/2012'
		GROUP BY team) AS subquery
	ORDER BY home_avg DESC
	LIMIT 3;
	```
	
## SubQuerys in SELECT
- Debe devolver un unico valor.
## Correlationship SubQuery
- Utiliza valores de la consulta externa para generar un resultado.
- Repite la ejecucion para cada fila generada en el conjunto de datos final.
- Se utiliza para la union avanzada, el filtrado y la evaluacion de datos

## Nested SubQuery

```
SELECT
	EXTRACT(MONTH FROM date) AS month,
	SUM(m.home_goal + m.away_goal) AS total_goals,
	SUM(m.home_goal + m.away_goal) -
	(SELECT AVG(goals)
	FROM (SELECT
			EXTRACT(MONTH FROM date) AS month,
			SUM(home_goal + away_goal) AS goals
			FROM match
			GROUP BY month) AS s) AS diff
	FROM match AS m
	GROUP BY month;
```


## CTE (Common Table Expressions)

```
WITH cte AS (
	SELECT col1, col2
	FROM table
	),
s2 AS(
	SELECT col1, col2
	FROM table
)
SELECT
	AVG(col1) AS avg_col
FROM cte;
```


## Windows Functions OVER

```
SELECT 
	date,
	(home_goal + away_goal) AS goals,
	AVG(home_goal + away_goal) OVER() AS overall_avg
FROM match
WHERE season = '2011/2012';
```

### RANK()

```
SELECT
	date,
	(home_goal + away_goal) AS goals,
	RANK() OVER(ORDER BY home_goal + away_goal DESC) AS goals_rank
FROM match
WHERE season = '2011/2012';
```

Windows Functions are processed after all sections of query, except ORDER BY
Windows Functions work with the result of query, and they don't use data since DB.
They available in many kind of db, except in SQLITE

## OVER with PARTITION

```
SELECT
	date, 
	(home_goal + away_goal) AS goals,
	AVG(home_goal + away_goal) OVER(PARTITION BY season) AS season_avg
	FROM match;
```

## Ventanas Deslizantes
- Realizar calculos relativos a la fila actual.
- Se puede utilizar para calcular totales acumulados, sumas, medias, etc.
- Se puede particionar por una o mas columnas 
```
ROWS BETWEEN <start> AND <finish>

PRECEDING -> para especificar el numero de filas antes o despues de un calculo
FOLLOWING
	UNBOUNDED PRECEDING -> incluir todas las filas desde el incio de los calculos
UNBOUNDED FOLLOWING
CURRENT ROW -> detener el calculo en la fila actual.

```

```
SELECT
	date, 
	home_goal, 
	away_goal,
	SUM(home_goal) 
		OVER(ORDER BY date ROWS BETWEEN 
			UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total
	FROM match
	WHERE hometeam_id =8456 AND season ='2011/2012';
```

## Decidir que tecnicas utillizar

### Uniones
- combinar mas de 2 tablas
- Operaciones sencillas/agregaciones
### Subconsultas correlacionadas
- Comparar subconsultas y tablas
- Evitar los limites de las uniones
- Tiempo de procesamiento
### Subconsultas multiples/anidadas
- Transformaciones en varios pasos
- Mejorar la exactitud y la reproducibilidad
### Expresiones de tabla comunes
- Organizar las subconsultas de forma secuencial
- Puedes hacer referencia a otros CTE.
- 