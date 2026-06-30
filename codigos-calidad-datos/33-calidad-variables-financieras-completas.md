# Calidad de Datos: Variables Financieras - Completas

## Descripción
Análisis completo de variables financieras de clientes jurídicos: activos, pasivos, ingresos y egresos con productos vigentes.

---

## Código SQL

```sql
DECLARE periodo_max_primas DATE;

SET periodo_max_primas = (
  SELECT MAX(DATE(FECHA_PROCESO))
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos`
);

WITH base AS (
  SELECT DISTINCT c.TIPO_DOCUMENTO, c.KEY_ID,
    c.VALOR_ACTIVO, c.VALOR_PASIVO,
    c.VALOR_INGRESOS, c.VALOR_EGRESOS
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos` p
  INNER JOIN `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_juridicos` c
    ON p.KEY_ID_TOMADOR = c.KEY_ID AND p.TIPO_DOCUMENTO_TOMADOR = c.TIPO_DOCUMENTO
  WHERE p.MCA_ANU_POLIZA != "S" AND DATE(p.FECHA_PROCESO) = periodo_max_primas
    AND DATE(p.FECHA_FIN_ENDOSO) >= CURRENT_DATE()
    AND DATE(p.FECHA_FIN_POLIZA) >= CURRENT_DATE()
    AND p.TIPO_DOCUMENTO_TOMADOR IN ("NT", "NE")
)

SELECT * FROM base;
```

---

**Categoría**: Calidad de Datos - Variables Financieras Completas  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29