# Calidad de Datos: Fecha de Constitución - Revisoria PJ

## Descripción
Extrae fecha de constitución de PJ en revisoria fiscal. Compara ARIS (FECHA_NACIMIENTO) contra Davivienda (FECHA_CONSTITUCION) con validación de rango (1901-actual).

---

## Código SQL

```sql
WITH base AS (
  SELECT DISTINCT
    c.TIPO_DOCUMENTO, c.KEY_ID, SUBSTR(CAST(c.KEY_ID AS STRING), 1, 9) AS KEY_ID_SIN_DV,
    CASE
      WHEN SAFE_CAST(SUBSTR(CAST(c.FECHA_NACIMIENTO AS STRING), 1, 10) AS DATE) < DATE('1901-01-01')
        OR SAFE_CAST(SUBSTR(CAST(c.FECHA_NACIMIENTO AS STRING), 1, 10) AS DATE) > CURRENT_DATE()
      THEN NULL
      ELSE SAFE_CAST(SUBSTR(CAST(c.FECHA_NACIMIENTO AS STRING), 1, 10) AS DATE)
    END AS FECHA_NACIMIENTO
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_trazabilidad_formatos_servicios_marcas` t
  LEFT JOIN `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_juridicos` c
    ON t.KEY_ID = c.KEY_ID AND t.TIPO_DOCUMENTO = c.TIPO_DOCUMENTO
  WHERE t.TIPO_DOCUMENTO IN ('NT', 'NE')
),

pj_davivienda AS (
  SELECT SUBSTR(CAST(KEY_ID AS STRING), 1, 9) AS KEY_ID_SIN_DV,
    CASE
      WHEN SAFE_CAST(SUBSTR(CAST(FECHA_CONSTITUCION AS STRING), 1, 10) AS DATE) < DATE('1901-01-01')
        OR SAFE_CAST(SUBSTR(CAST(FECHA_CONSTITUCION AS STRING), 1, 10) AS DATE) > CURRENT_DATE()
      THEN NULL
      ELSE SAFE_CAST(SUBSTR(CAST(FECHA_CONSTITUCION AS STRING), 1, 10) AS DATE)
    END AS FECHA_CONSTITUCION
  FROM `sb-ecosistemaanalitico-lago.davivienda.t_pj_datos_basicos`
)

SELECT b.TIPO_DOCUMENTO, b.KEY_ID, b.FECHA_NACIMIENTO, d.FECHA_CONSTITUCION
FROM base b
LEFT JOIN pj_davivienda d ON b.KEY_ID_SIN_DV = d.KEY_ID_SIN_DV;
```

---

**Categoría**: Calidad de Datos - Revisoria Fiscal  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29