# Calidad de Datos: Actividad Económica - Casos Sin Davivienda

## Descripción
Identifica clientes con productos vigentes que faltande información de actividad económica **tanto en ARIS como en Davivienda**. Casos más críticos de ausencia de datos.

---

## Código SQL

```sql
DECLARE periodo_max_primas DATE;

SET periodo_max_primas = (
  SELECT MAX(DATE(FECHA_PROCESO))
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos`
);

WITH base AS (
  SELECT DISTINCT c.TIPO_DOCUMENTO, c.KEY_ID, c.CODIGO_CIIU, c.ACTIVIDAD_ECONOMICA
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos` p
  INNER JOIN `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_juridicos` c
    ON p.KEY_ID_TOMADOR = c.KEY_ID AND p.TIPO_DOCUMENTO_TOMADOR = c.TIPO_DOCUMENTO
  WHERE p.MCA_ANU_POLIZA != "S" AND DATE(p.FECHA_PROCESO) = periodo_max_primas
    AND DATE(p.FECHA_FIN_ENDOSO) >= CURRENT_DATE()
    AND DATE(p.FECHA_FIN_POLIZA) >= CURRENT_DATE()
    AND p.TIPO_DOCUMENTO_TOMADOR IN ("NT","NE")
),

pj_davivienda AS (
  SELECT SUBSTR(CAST(KEY_ID AS STRING), 1, 9) AS KEY_ID_SIN_DV,
    ACTIVIDAD_ECONOMICA AS ACTIVIDAD_ECONOMICA_DAV, COD_ACTIVIDAD_ECONOMICA
  FROM `sb-ecosistemaanalitico-lago.davivienda.t_pj_datos_basicos`
)

SELECT * FROM base b
LEFT JOIN pj_davivienda d ON SUBSTR(CAST(b.KEY_ID AS STRING), 1, 9) = d.KEY_ID_SIN_DV
WHERE (b.CODIGO_CIIU IS NULL OR b.ACTIVIDAD_ECONOMICA IS NULL)
  AND (d.COD_ACTIVIDAD_ECONOMICA IS NULL OR d.ACTIVIDAD_ECONOMICA_DAV IS NULL);
```

---

**Categoría**: Calidad de Datos - Gaps Críticos  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29