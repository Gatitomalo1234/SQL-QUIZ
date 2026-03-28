# QUIZ SQL - RESULTADOS

---

## 🔍 PUNTO 1: Exploración Inicial

### 📋 Consulta 1: Vista General de Datos
Esta consulta permite visualizar todo el contenido de la **TABLA23**.
```sql
SELECT * 
FROM TABLA23;
```

### 📋 Consulta 2: Actividades Únicas
Obtiene el listado sin repeticiones de los nombres de las actividades registradas.
```sql
SELECT DISTINCT actividad_nombre 
FROM TABLA23;
```

---

## 🏢 PUNTO 2: Reporte Detallado de Sedes
Extracción de información específica de la **TABLA22**, renombrando campos para mayor claridad y limitando el resultado a los primeros 50 registros ordenados por sede.

```sql
SELECT 
    sede_codigo AS codigo_sede, 
    periodo_anio AS anio_reporte, 
    actividad_codigo AS cod_actividad, 
    actividad_nombre AS nombre_actividad
FROM TABLA22
ORDER BY sede_codigo ASC
LIMIT 50; 
```

---

## 🛠️ PUNTO 3: Creación de Estructura (DDL)
Definición de la tabla resumen para el almacenamiento consolidado de las sedes TIC.

```sql
CREATE TABLE tic_sedes_resumen (
    resumen_id INTEGER, 
    sede_codigo INTEGER, 
    anio INTEGER, 
    Total_actividades INTEGER, 
    Tiene_internet BOOLEAN,
    Fecha_carga DATE, 
    UNIQUE(sede_codigo, anio)
);
```

---

## 📍 PUNTO 4: Análisis por Departamento
Filtro de sedes con actividad específica (cod 5), agrupadas por los dos primeros dígitos del código (departamento) y filtrando aquellos con más de 500 sedes únicas.

```sql
SELECT 
    SUBSTR(sede_codigo, 1, 2) AS codigo_departamento, 
    COUNT(DISTINCT sede_codigo) AS total_sedes_unicas 
FROM TABLA23
WHERE actividad_codigo = 5 
GROUP BY codigo_departamento
HAVING total_sedes_unicas > 500 
ORDER BY total_sedes_unicas DESC; 
```

---

## 📈 PUNTO 5: Análisis de Tendencia (2022 vs 2023)
Comparativa avanzada utilizando **CTEs** para medir el crecimiento o decrecimiento de actividades por sede entre ambos periodos.

```sql
WITH resumen_2022 AS (
    SELECT sede_codigo, COUNT(*) AS total_act_2022
    FROM TABLA22
    GROUP BY sede_codigo
),
resumen_2023 AS (
    SELECT sede_codigo, COUNT(*) AS total_act_2023
    FROM TABLA23
    GROUP BY sede_codigo
)
SELECT 
    r22.sede_codigo,
    r22.total_act_2022,
    r23.total_act_2023,
    (r23.total_act_2023 - r22.total_act_2022) AS diferencia,
    CASE 
        WHEN (r23.total_act_2023 - r22.total_act_2022) > 0 THEN 'CRECIÓ'
        WHEN (r23.total_act_2023 - r22.total_act_2022) < 0 THEN 'DECRECIÓ'
        ELSE 'SIN CAMBIO'
    END AS tendencia
FROM resumen_2022 AS r22
INNER JOIN resumen_2023 AS r23 
    ON r22.SEDE_CODIGO = r23.SEDE_CODIGO
WHERE r22.total_act_2022 >= 2 
ORDER BY diferencia DESC 
LIMIT 30;
```

