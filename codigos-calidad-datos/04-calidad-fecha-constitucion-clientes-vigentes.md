# Calidad de Datos: Fecha de Constitución - Clientes Vigentes

## Descripción
Extrae la fecha de constitución de clientes jurídicos (NT/NE) con productos vigentes en Seguros Bolívar, normalizando con datos de Davivienda. Proporciona un universo completo de clientes activos para auditoría y análisis de antigüedad empresarial.

## Fuentes de Datos
- **Base Principal**: `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos`
- **Clientes Jurídicos**: `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_juridicos`
- **Datos Davivienda**: `sb-ecosistemaanalitico-lago.davivienda.t_pj_datos_basicos`

## Lógica de Consulta

### 1-4. Variables y Base
- Define fecha máxima de proceso
- Extrae clientes jurídicos vigentes
- Normaliza datos de Davivienda a 9 dígitos

### 5. Unión y Deduplicación
- LEFT JOIN entre base ARIS y Davivienda
- ROW_NUMBER() por NIT para 1 registro por cliente
- Prioriza registros con fecha en Davivienda
- Ordena por fecha descendente (más recientes primero)

## Campos de Salida

| Campo | Descripción |
|-------|-------------|
| `TIPO_DOCUMENTO` | Tipo de identificación (NT/NE) |
| `KEY_ID` | NIT del cliente |
| `NOMBRE` | Nombre del cliente jurídico |
| `FECHA_NACIMIENTO` | Fecha de constitución según ARIS |
| `COD_TIPO_IDENTIFICACION` | Código tipo ID (Davivienda) |
| `TIPO_IDENTIFICACION` | Descripción tipo ID (Davivienda) |
| `FECHA_CONSTITUCION` | Fecha de constitución según Davivienda |

## Casos de Uso
- **Auditoría de antigüedad**: Identificar empresas jóvenes vs consolidadas
- **Análisis de riesgo**: Correlacionar antigüedad con perfiles de riesgo
- **Validación de datos**: Comparar fechas entre ARIS y Davivienda
- **Enriquecimiento de datos**: Completar campos faltantes con Davivienda

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
    c.NOMBRE,
    c.FECHA_NACIMIENTO
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos` p
  INNER JOIN `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_juridicos` c
    ON p.KEY_ID_TOMADOR = c.KEY_ID
   AND p.TIPO_DOCUMENTO_TOMADOR = c.TIPO_DOCUMENTO
  WHERE p.MCA_ANU_POLIZA != "S"
    AND DATE(p.FECHA_PROCESO) = periodo_max_primas
    AND DATE(p.FECHA_FIN_ENDOSO) >= CURRENT_DATE()
    AND DATE(p.FECHA_FIN_POLIZA) >= CURRENT_DATE()
    AND p.TIPO_DOCUMENTO_TOMADOR IN ("NT","NE")
),

-- ============================================================
-- 4. TABLA DAVIVIENDA (NORMALIZADA A 9 DÍGITOS)
-- ============================================================
pj_davivienda AS (
  SELECT
    COD_TIPO_IDENTIFICACION,
    TIPO_IDENTIFICACION,
    SUBSTR(CAST(KEY_ID AS STRING), 1, 9) AS KEY_ID_SIN_DV,
    FECHA_CONSTITUCION
  FROM `sb-ecosistemaanalitico-lago.davivienda.t_pj_datos_basicos`
)

-- ============================================================
-- 5. RESULTADO FINAL (1 REGISTRO POR NIT)
-- ============================================================
SELECT
  TIPO_DOCUMENTO,
  KEY_ID,
  NOMBRE,
  FECHA_NACIMIENTO,
  COD_TIPO_IDENTIFICACION,
  TIPO_IDENTIFICACION,
  FECHA_CONSTITUCION
FROM (
  SELECT
    b.TIPO_DOCUMENTO,
    b.KEY_ID,
    b.NOMBRE,
    b.FECHA_NACIMIENTO,
    d.COD_TIPO_IDENTIFICACION,
    d.TIPO_IDENTIFICACION,
    d.FECHA_CONSTITUCION,

    -- Control de duplicados por NIT
    ROW_NUMBER() OVER (
      PARTITION BY b.KEY_ID 
      ORDER BY 
        -- prioriza registros con info de Davivienda
        CASE WHEN d.FECHA_CONSTITUCION IS NOT NULL THEN 1 ELSE 2 END,
        d.FECHA_CONSTITUCION DESC
    ) AS rn

  FROM base b
  LEFT JOIN pj_davivienda d
    ON b.KEY_ID_SIN_DV = d.KEY_ID_SIN_DV
)
WHERE rn = 1;
```

---

**Categoría**: Calidad de Datos - Validación de Datos Maestros  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29