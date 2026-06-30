# Calidad de Datos: Actividad Económica - Revisoria PJ

## Descripción
Extrae actividad económica de personas jurídicas en revisoria fiscal desde tabla de trazabilidad. Normaliza con Davivienda tratando código '-3' como NULL (dato inválido).

---

## Código SQL

```sql
WITH base AS (
  SELECT DISTINCT
    TIPO_DOCUMENTO,
    KEY_ID,
    SUBSTR(CAST(KEY_ID AS STRING), 1, 9) AS KEY_ID_SIN_DV,

    CASE
      WHEN CODIGO_ACTIVIDAD_ECONOMICA = '-3' THEN NULL
      ELSE CODIGO_ACTIVIDAD_ECONOMICA
    END AS CODIGO_CIIU,

    CASE
      WHEN CODIGO_ACTIVIDAD_ECONOMICA = '-3' THEN NULL
      ELSE ACTIVIDAD_ECONOMICA
    END AS ACTIVIDAD_ECONOMICA

  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_trazabilidad_formatos_servicios_marcas`
  WHERE TIPO_DOCUMENTO IN ('NT', 'NE')
),

pj_davivienda AS (
  SELECT
    COD_TIPO_IDENTIFICACION,
    TIPO_IDENTIFICACION,
    SUBSTR(CAST(KEY_ID AS STRING), 1, 9) AS KEY_ID_SIN_DV,
    ACTIVIDAD_ECONOMICA AS ACTIVIDAD_ECONOMICA_DAV,
    COD_ACTIVIDAD_ECONOMICA
  FROM `sb-ecosistemaanalitico-lago.davivienda.t_pj_datos_basicos`
)

SELECT
  TIPO_DOCUMENTO,
  KEY_ID,
  CODIGO_CIIU,
  ACTIVIDAD_ECONOMICA,
  COD_ACTIVIDAD_ECONOMICA,
  ACTIVIDAD_ECONOMICA_DAV

FROM (
  SELECT
    b.TIPO_DOCUMENTO,
    b.KEY_ID,
    b.CODIGO_CIIU,
    b.ACTIVIDAD_ECONOMICA,

    d.COD_TIPO_IDENTIFICACION,
    d.TIPO_IDENTIFICACION,
    d.COD_ACTIVIDAD_ECONOMICA,
    d.ACTIVIDAD_ECONOMICA_DAV,

    ROW_NUMBER() OVER (
      PARTITION BY b.KEY_ID
      ORDER BY
        CASE WHEN d.ACTIVIDAD_ECONOMICA_DAV IS NOT NULL THEN 1 ELSE 2 END,
        d.COD_ACTIVIDAD_ECONOMICA DESC
    ) AS rn

  FROM base b
  LEFT JOIN pj_davivienda d
    ON b.KEY_ID_SIN_DV = d.KEY_ID_SIN_DV
)
WHERE rn = 1;
```

---

**Categoría**: Calidad de Datos - Revisoria Fiscal  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29