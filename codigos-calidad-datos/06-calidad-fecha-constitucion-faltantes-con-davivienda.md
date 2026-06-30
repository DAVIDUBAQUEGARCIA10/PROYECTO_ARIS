# Calidad de Datos: Fecha de Constitución - Faltantes con Datos en Davivienda

## Descripción
Identifica clientes jurídicos con productos vigentes que tienen FECHA_NACIMIENTO nula en ARIS **PERO que SÍ tienen FECHA_CONSTITUCION en Davivienda**. Subconjunto prioritario para remedición automática.

## Diferencia Clave con Archivo B
- **Archivo B**: Clientes con FECHA_NACIMIENTO NULL (todos)
- **Archivo C**: Clientes con FECHA_NACIMIENTO NULL Y datos en Davivienda (remediables)

## Fuentes de Datos
- **Base Principal**: `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos`
- **Clientes Jurídicos**: `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_juridicos`
- **Datos Davivienda**: `sb-ecosistemaanalitico-lago.davivienda.t_pj_datos_basicos`

## Lógica de Consulta

### Criterio Clave Dual (Línea 81)
```sql
WHERE b.FECHA_NACIMIENTO IS NULL 
  AND d.FECHA_CONSTITUCION IS NOT NULL
```

Retorna SOLO clientes donde:
1. ARIS tiene FECHA_NACIMIENTO nula
2. Davivienda tiene FECHA_CONSTITUCION disponible

## Campos de Salida

| Campo | Descripción |
|-------|-------------|
| `FECHA_NACIMIENTO` | NULL en ARIS |
| `FECHA_CONSTITUCION` | Valor confiable en Davivienda → Usar para llenar vacío |

## Casos de Uso
- **Remedición prioritaria**: Estos registros pueden actualizarse inmediatamente
- **Data governance**: Automatizar población de campos con fuente confiable
- **Validación cruzada**: Confirmar cobertura de Davivienda
- **Reporting de calidad**: Métricas de gaps remediables vs no remediables

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
  -- FILTRO CLAVE: Faltantes en ARIS pero disponibles en Davivienda
  WHERE b.FECHA_NACIMIENTO IS NULL 
    AND d.FECHA_CONSTITUCION IS NOT NULL
)
WHERE rn = 1;
```

---

**Categoría**: Calidad de Datos - Plan de Remedición  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29  
**Prioridad**: ALTA - Remediables inmediatamente