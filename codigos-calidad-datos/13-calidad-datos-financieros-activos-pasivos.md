# Calidad de Datos: Datos Financieros - Activos y Pasivos

## Descripción
Identifica clientes jurídicos con productos vigentes que tienen **valores financieros insuficientes o nulos**. Filtra por VALOR_ACTIVO, VALOR_PASIVO, VALOR_INGRESOS y VALOR_EGRESOS todos ≤ 1000 o NULL. Indicador de información financiera incompleta o empresas sin actividad financiera reportada.

## Fuentes de Datos
- `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos`
- `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_juridicos`

## Lógica de Consulta

Filtra clientes donde:
- VALOR_ACTIVO ≤ 1000 O NULL
- VALOR_PASIVO ≤ 1000 O NULL
- VALOR_INGRESOS ≤ 1000 O NULL
- VALOR_EGRESOS ≤ 1000 O NULL

Criterio AND (todos deben cumplirse) indica información financiera muy débil.

## Campos de Salida
- `TIPO_DOCUMENTO`: Tipo doc
- `KEY_ID`: NIT
- `KEY_ID_SIN_DV`: NIT normalizado
- `VALOR_ACTIVO`: Activos totales
- `VALOR_PASIVO`: Pasivos totales
- `VALOR_INGRESOS`: Ingresos
- `VALOR_EGRESOS`: Egresos

## Casos de Uso
- **Auditoría de calidad**: Detectar clientes sin datos financieros
- **Análisis de riesgo**: Empresas sin data sugieren riesgo
- **Validación**: Identificar estructuras sin reportes financieros
- **Cumplimiento**: Documentación financiera requerida

---

## Código SQL

```sql
-- ============================================================
-- 1. DECLARAR VARIABLE
-- ============================================================
DECLARE periodo_max_primas DATE;

-- ============================================================
-- 2. ASIGNAR VALOR (FECHA MÁXIMA DE PROCESO)
-- ============================================================
SET periodo_max_primas = (
  SELECT MAX(DATE(FECHA_PROCESO))
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos`
);

-- ============================================================
-- 3. BASE PRINCIPAL (CLIENTES CON PRODUCTO VIGENTE)
-- ============================================================
WITH base AS (
  SELECT DISTINCT
    c.TIPO_DOCUMENTO,
    c.KEY_ID,
    SUBSTR(CAST(c.KEY_ID AS STRING), 1, 9) AS KEY_ID_SIN_DV,
    c.VALOR_ACTIVO, 
    c.VALOR_PASIVO, 
    c.VALOR_INGRESOS, 
    c.VALOR_EGRESOS
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos` p
  INNER JOIN `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_juridicos` c
    ON p.KEY_ID_TOMADOR = c.KEY_ID
   AND p.TIPO_DOCUMENTO_TOMADOR = c.TIPO_DOCUMENTO
  WHERE p.MCA_ANU_POLIZA != "S"
    AND DATE(p.FECHA_PROCESO) = periodo_max_primas
    AND DATE(p.FECHA_FIN_ENDOSO) >= CURRENT_DATE()
    AND DATE(p.FECHA_FIN_POLIZA) >= CURRENT_DATE()
    AND p.TIPO_DOCUMENTO_TOMADOR IN ("NT", "NE")
)

-- ============================================================
-- 4. RESULTADO FINAL
-- ============================================================
SELECT *
FROM base
WHERE 
  (VALOR_ACTIVO <= 1000 OR VALOR_ACTIVO IS NULL)
  AND (VALOR_PASIVO <= 1000 OR VALOR_PASIVO IS NULL)
  AND (VALOR_INGRESOS <= 1000 OR VALOR_INGRESOS IS NULL)
  AND (VALOR_EGRESOS <= 1000 OR VALOR_EGRESOS IS NULL);
```

---

**Categoría**: Calidad de Datos - Auditoría Financiera  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29