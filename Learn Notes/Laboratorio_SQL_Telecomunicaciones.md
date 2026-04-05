# Laboratorio SQL — Telecomunicaciones
> **Nivel:** Intermedio–Avanzado · **Motor:** PostgreSQL 13+ · **Datos:** `telecom_data.sql`

---

## Esquema de tablas

```sql
-- Registro de sesiones por suscriptor (particionada por fecha)
cdr_subscribers (
    subscriber_id     BIGINT,        -- PK
    msisdn            VARCHAR(15),
    fecha             DATE,          -- columna de partición
    timestamp_inicio  TIMESTAMP,
    volumen_mb        NUMERIC(10,2),
    duracion_seg      INTEGER,
    tecnologia        VARCHAR(3),    -- '4G' | '5G'
    celda_id          VARCHAR(20),
    region            VARCHAR(20)
)

-- Consumo por aplicación / servicio (particionada por fecha)
cdr_services (
    subscriber_id      BIGINT,       -- FK → cdr_subscribers
    fecha              DATE,         -- columna de partición
    servicio           VARCHAR(40),  -- 'WhatsApp', 'YouTube', etc.
    categoria          VARCHAR(20),  -- 'social' | 'video' | 'gaming' | ...
    bytes_descargados  BIGINT,
    bytes_subidos      BIGINT,
    num_sesiones       INTEGER
)
```

---

## Ejercicio 1 — Ranking de consumo por región

**Caso de uso:** El equipo comercial necesita un reporte mensual con los 5 suscriptores que más datos consumieron en cada región. Si dos suscriptores tienen el mismo consumo deben compartir posición. El reporte debe incluir el consumo total en GB y la tecnología predominante usada.

**Conceptos:** `RANK()` · `PARTITION BY` · `MODE() WITHIN GROUP` · CTE

> [!tip] Pista
> Usa `RANK()` con `PARTITION BY region` y filtra con una CTE para quedarte solo con `rank <= 5`. Para la tecnología predominante, `MODE() WITHIN GROUP (ORDER BY tecnologia)` devuelve el valor más frecuente de una columna dentro de un grupo.

```sql
WITH consumo_mensual AS (
  SELECT
    subscriber_id,
    region,
    SUM(volumen_mb) / 1024.0                              AS consumo_gb,
    MODE() WITHIN GROUP (ORDER BY tecnologia)             AS tec_predominante,
    RANK() OVER (
      PARTITION BY region
      ORDER BY SUM(volumen_mb) DESC
    )                                                     AS ranking
  FROM   cdr_subscribers
  WHERE  fecha BETWEEN '2024-01-01' AND '2024-01-31'
  GROUP BY subscriber_id, region
)
SELECT subscriber_id, region, consumo_gb, tec_predominante, ranking
FROM   consumo_mensual
WHERE  ranking <= 5
ORDER BY region, ranking;
```

---

## Ejercicio 2 — Detección de caída de consumo (churn signals)

**Caso de uso:** El área de retención quiere identificar suscriptores en riesgo de abandono. Un suscriptor es candidato a churn si su consumo mensual cayó más de un 40 % respecto al mes anterior. Muestra el consumo actual, el anterior y el porcentaje de caída.

**Conceptos:** `LAG()` · `PARTITION BY` · `NULLIF()` · CTE

> [!tip] Pista
> Agrega primero por `(subscriber_id, mes)`, luego usa `LAG()` para traer el consumo del mes previo. El porcentaje de caída es `(actual - anterior) / anterior * 100`. Usa `NULLIF(anterior, 0)` para evitar división por cero cuando un suscriptor no tenía consumo el mes previo.

```sql
WITH consumo_mes AS (
  SELECT
    subscriber_id,
    DATE_TRUNC('month', fecha)        AS mes,
    SUM(volumen_mb)                   AS consumo_mb
  FROM   cdr_subscribers
  GROUP BY subscriber_id, mes
),
con_lag AS (
  SELECT
    subscriber_id,
    mes,
    consumo_mb,
    LAG(consumo_mb) OVER (
      PARTITION BY subscriber_id
      ORDER BY mes
    )                                 AS consumo_mes_ant,
    ROUND(
      (consumo_mb - LAG(consumo_mb) OVER (
        PARTITION BY subscriber_id ORDER BY mes)
      ) * 100.0
      / NULLIF(LAG(consumo_mb) OVER (
          PARTITION BY subscriber_id ORDER BY mes), 0)
    , 2)                              AS pct_cambio
  FROM   consumo_mes
)
SELECT *
FROM   con_lag
WHERE  pct_cambio < -40
ORDER BY pct_cambio;
```

---

## Ejercicio 3 — Servicio estrella por suscriptor

**Caso de uso:** Marketing necesita saber cuál es la aplicación principal (mayor bytes descargados) de cada suscriptor y qué porcentaje representa del total de su consumo. También clasifica ese porcentaje: `'Dominante'` (>70 %), `'Principal'` (40–70 %) o `'Compartido'` (<40 %).

**Conceptos:** `RANK()` · `SUM() OVER` anidado · `CASE WHEN` · CTE

> [!tip] Pista
> Usa `RANK()` particionado por `subscriber_id` ordenado por bytes descendente para identificar el servicio top. Para el porcentaje, necesitas el total del suscriptor: `SUM(SUM(bytes_descargados)) OVER (PARTITION BY subscriber_id)` — nota el doble `SUM`, el interno agrega por grupo y el externo es la window function.

```sql
WITH svc_ranked AS (
  SELECT
    subscriber_id,
    servicio,
    SUM(bytes_descargados)              AS bytes_svc,
    SUM(SUM(bytes_descargados)) OVER (
      PARTITION BY subscriber_id
    )                                   AS bytes_total,
    RANK() OVER (
      PARTITION BY subscriber_id
      ORDER BY SUM(bytes_descargados) DESC
    )                                   AS rk
  FROM   cdr_services
  WHERE  fecha >= '2024-01-01'
  GROUP BY subscriber_id, servicio
)
SELECT
  subscriber_id,
  servicio                              AS app_top,
  ROUND(bytes_svc / 1e9, 2)            AS gb_descargados,
  ROUND(bytes_svc * 100.0 / bytes_total, 1) AS pct_consumo,
  CASE
    WHEN bytes_svc * 100.0 / bytes_total > 70 THEN 'Dominante'
    WHEN bytes_svc * 100.0 / bytes_total > 40 THEN 'Principal'
    ELSE 'Compartido'
  END                                   AS perfil_uso
FROM   svc_ranked
WHERE  rk = 1;
```

---

## Ejercicio 4 — Media móvil de consumo semanal

**Caso de uso:** El equipo de red necesita monitorear la tendencia de consumo semanal por región usando una media móvil de 4 semanas para suavizar picos puntuales. Incluye también el total acumulado desde inicio de año.

**Conceptos:** `AVG() OVER` · `SUM() OVER` · `ROWS BETWEEN` · `UNBOUNDED PRECEDING`

> [!tip] Pista
> Agrega primero por `(region, semana)` en una CTE. Luego aplica **dos** window functions sobre esa tabla: una con `ROWS BETWEEN 3 PRECEDING AND CURRENT ROW` para la MA-4, y otra con `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW` para el acumulado anual. Ambas deben ir `PARTITION BY region ORDER BY semana`.

```sql
WITH semanas AS (
  SELECT
    region,
    DATE_TRUNC('week', fecha)          AS semana,
    SUM(volumen_mb) / 1024.0           AS consumo_gb
  FROM   cdr_subscribers
  WHERE  fecha >= DATE_TRUNC('year', CURRENT_DATE)
  GROUP BY region, semana
)
SELECT
  region,
  semana,
  consumo_gb,
  ROUND(
    AVG(consumo_gb) OVER (
      PARTITION BY region
      ORDER BY semana
      ROWS BETWEEN 3 PRECEDING AND CURRENT ROW
    ), 2)                              AS ma_4semanas,
  ROUND(
    SUM(consumo_gb) OVER (
      PARTITION BY region
      ORDER BY semana
      ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ), 2)                              AS acumulado_ytd
FROM   semanas
ORDER BY region, semana;
```

---

## Ejercicio 5 — Análisis de migración 4G → 5G

**Caso de uso:** El equipo de producto quiere medir el impacto del upgrade a 5G. Identifica suscriptores que en los últimos 3 meses pasaron de usar solo 4G a usar 5G, y compara su consumo promedio mensual antes y después del cambio.

**Conceptos:** CTE encadenadas · `EXISTS` correlacionado · `AVG() FILTER (WHERE ...)` · `MIN()`

> [!tip] Pista
> Necesitas tres CTEs encadenadas: (1) agrega consumo por `(subscriber_id, mes, tecnologia)`, (2) detecta el primer mes con 5G usando `MIN(mes)`, (3) filtra solo quienes tenían 4G antes de esa fecha con `EXISTS`. En el `SELECT` final usa `AVG(...) FILTER (WHERE mes < fecha_migracion)` para promediar antes y después en una sola pasada, sin subqueries extra.

```sql
WITH uso_mensual AS (
  SELECT
    subscriber_id,
    DATE_TRUNC('month', fecha)         AS mes,
    tecnologia,
    SUM(volumen_mb)                    AS consumo_mb
  FROM   cdr_subscribers
  GROUP BY subscriber_id, mes, tecnologia
),
primer_5g AS (
  SELECT
    subscriber_id,
    MIN(mes) AS mes_migracion
  FROM   uso_mensual
  WHERE  tecnologia = '5G'
  GROUP BY subscriber_id
),
-- Solo suscriptores que antes usaban 4G
migrantes AS (
  SELECT p.subscriber_id, p.mes_migracion
  FROM   primer_5g p
  WHERE  EXISTS (
    SELECT 1 FROM uso_mensual u
    WHERE  u.subscriber_id = p.subscriber_id
      AND  u.tecnologia    = '4G'
      AND  u.mes           < p.mes_migracion
  )
)
SELECT
  u.subscriber_id,
  m.mes_migracion,
  ROUND(AVG(u.consumo_mb) FILTER (WHERE u.mes < m.mes_migracion), 0)
                                       AS avg_mb_antes_4g,
  ROUND(AVG(u.consumo_mb) FILTER (WHERE u.mes >= m.mes_migracion), 0)
                                       AS avg_mb_despues_5g
FROM   uso_mensual u
JOIN   migrantes m USING (subscriber_id)
GROUP BY u.subscriber_id, m.mes_migracion
ORDER BY avg_mb_despues_5g DESC;
```

---

## Ejercicio 6 — Congestionamiento de celdas por hora

**Caso de uso:** Operaciones necesita identificar las 10 celdas con mayor pico de tráfico, la hora del día en que ocurre ese pico, y clasificarlas por nivel de urgencia: `'Crítica'` (>percentil 95 del tráfico global), `'Alta'` (p80–p95) o `'Normal'`.

**Conceptos:** `RANK()` · `PERCENTILE_CONT()` · `EXTRACT()` · `CASE WHEN` · CTE

> [!tip] Pista
> Primero agrega por `(celda_id, hora)` y usa `RANK()` para quedarte con la hora pico de cada celda. Luego, en una CTE separada, calcula los percentiles globales con `PERCENTILE_CONT(0.95) WITHIN GROUP (ORDER BY trafico_mb)`. Finalmente cruza ambas CTEs con un `CROSS JOIN` implícito (coma en el `FROM`) para comparar cada celda contra los umbrales.

```sql
WITH trafico_hora AS (
  SELECT
    celda_id,
    EXTRACT(HOUR FROM timestamp_inicio)  AS hora,
    SUM(volumen_mb)                      AS trafico_mb,
    COUNT(*)                             AS num_sesiones
  FROM   cdr_subscribers
  WHERE  fecha >= CURRENT_DATE - INTERVAL '30 days'
  GROUP BY celda_id, hora
),
pico_celda AS (
  SELECT *,
    RANK() OVER (
      PARTITION BY celda_id
      ORDER BY trafico_mb DESC
    )                                    AS rk
  FROM   trafico_hora
),
percentiles AS (
  SELECT
    PERCENTILE_CONT(0.95) WITHIN GROUP
      (ORDER BY trafico_mb)              AS p95,
    PERCENTILE_CONT(0.80) WITHIN GROUP
      (ORDER BY trafico_mb)              AS p80
  FROM   pico_celda
  WHERE  rk = 1
)
SELECT
  p.celda_id,
  p.hora                                 AS hora_pico,
  ROUND(p.trafico_mb / 1024.0, 2)       AS trafico_gb,
  p.num_sesiones,
  CASE
    WHEN p.trafico_mb >= x.p95 THEN 'Crítica'
    WHEN p.trafico_mb >= x.p80 THEN 'Alta'
    ELSE 'Normal'
  END                                    AS nivel_urgencia
FROM   pico_celda p, percentiles x
WHERE  p.rk = 1
ORDER BY p.trafico_mb DESC
LIMIT  10;
```

---

## Ejercicio 7 — Segmentación de usuarios por perfil de apps

**Caso de uso:** El área de productos quiere segmentar suscriptores en perfiles: `'Streamer'` (video >50 % del consumo), `'Social'` (redes sociales >50 %), `'Gamer'` (gaming >30 %), `'Navegador'` (el resto). Si un usuario califica en más de uno, asígnale el de mayor porcentaje.

**Conceptos:** `SUM() OVER` anidado · `RANK()` · `CASE WHEN` · CTE encadenadas

> [!tip] Pista
> En la primera CTE usa `SUM(SUM(...)) OVER (PARTITION BY subscriber_id)` para obtener el total de bytes del suscriptor sin salir del `GROUP BY`. Añade `RANK()` para identificar la categoría dominante. En la segunda CTE filtra `rk = 1` y aplica el `CASE WHEN` con los umbrales de cada segmento.

```sql
WITH pct_categoria AS (
  SELECT
    subscriber_id,
    categoria,
    SUM(bytes_descargados + bytes_subidos)      AS bytes_cat,
    SUM(SUM(bytes_descargados + bytes_subidos)) OVER (
      PARTITION BY subscriber_id
    )                                           AS bytes_total,
    RANK() OVER (
      PARTITION BY subscriber_id
      ORDER BY SUM(bytes_descargados + bytes_subidos) DESC
    )                                           AS rk
  FROM   cdr_services
  WHERE  fecha >= DATE_TRUNC('month', CURRENT_DATE)
  GROUP BY subscriber_id, categoria
),
categoria_top AS (
  SELECT
    subscriber_id,
    categoria,
    ROUND(bytes_cat * 100.0 / bytes_total, 1)  AS pct
  FROM   pct_categoria
  WHERE  rk = 1
)
SELECT
  subscriber_id,
  categoria                                     AS categoria_top,
  pct,
  CASE
    WHEN categoria = 'video'   AND pct > 50 THEN 'Streamer'
    WHEN categoria = 'social'  AND pct > 50 THEN 'Social'
    WHEN categoria = 'gaming'  AND pct > 30 THEN 'Gamer'
    ELSE 'Navegador'
  END                                           AS segmento
FROM   categoria_top
ORDER BY pct DESC;
```

---

## Ejercicio 8 — Reporte ejecutivo con ROLLUP

**Caso de uso:** La dirección pide un cubo de datos mensual con el total de GB consumidos agrupado jerárquicamente por `región → tecnología → mes`, incluyendo subtotales automáticos en cada nivel. Las filas de subtotal deben mostrarse con etiquetas legibles en lugar de `NULL`.

**Conceptos:** `GROUP BY ROLLUP` · `COALESCE()` · `TO_CHAR()` · `NULLS LAST`

> [!tip] Pista
> `GROUP BY ROLLUP(a, b, c)` genera automáticamente los subtotales: primero por `(a,b,c)`, luego `(a,b)`, luego `(a)` y finalmente el total general. Cada nivel de subtotal aparece con `NULL` en las columnas que colapsa. Usa `COALESCE(columna, 'ETIQUETA')` para reemplazarlos. El orden de las columnas en `ROLLUP` define la jerarquía, de mayor a menor granularidad.

```sql
SELECT
  COALESCE(region,      'TOTAL GENERAL')  AS region,
  COALESCE(tecnologia,  'TOTAL REGIÓN')   AS tecnologia,
  COALESCE(
    TO_CHAR(DATE_TRUNC('month', fecha), 'YYYY-MM'),
    'TOTAL TEC'
  )                                        AS mes,
  ROUND(SUM(volumen_mb) / 1024.0, 2)     AS total_gb,
  COUNT(DISTINCT subscriber_id)           AS suscriptores_activos,
  ROUND(AVG(volumen_mb), 1)              AS avg_mb_sesion
FROM   cdr_subscribers
WHERE  fecha >= DATE_TRUNC('year', CURRENT_DATE)
GROUP BY ROLLUP (
  region,
  tecnologia,
  DATE_TRUNC('month', fecha)
)
ORDER BY
  region     NULLS LAST,
  tecnologia NULLS LAST,
  mes        NULLS LAST;
```

---

## Referencia rápida de patrones

| Patrón | Cuándo usarlo |
|---|---|
| `RANK() OVER (PARTITION BY x ORDER BY y DESC)` | Rankings independientes por grupo |
| `LAG(col) OVER (PARTITION BY x ORDER BY fecha)` | Comparar con el periodo anterior |
| `SUM(SUM(col)) OVER (PARTITION BY x)` | Total del grupo dentro de un `GROUP BY` |
| `AVG(col) OVER (... ROWS BETWEEN 3 PRECEDING AND CURRENT ROW)` | Media móvil de N periodos |
| `PERCENTILE_CONT(0.95) WITHIN GROUP (ORDER BY col)` | Percentiles para umbrales dinámicos |
| `AGG(col) FILTER (WHERE condicion)` | Agregado condicional sin subquery |
| `GROUP BY ROLLUP(a, b, c)` | Subtotales jerárquicos automáticos |
| `COALESCE(col, 'etiqueta')` | Reemplazar NULLs de ROLLUP/CUBE |

---

*Generado para práctica de Data Engineering · PostgreSQL · 2024*
