# Calidad de Datos: Actividad Económica - Campos Faltantes

## Descripción
Identifica clientes jurídicos con productos vigentes que tienen **campos faltantes** en su información de actividad económica (CODIGO_CIIU y/o ACTIVIDAD_ECONOMICA). Intenta complementar la información con datos de Davivienda cuando estén disponibles. Útil para auditoría de calidad de datos y planes de remedición.

## Fuentes de Datos
- **Base Principal**: `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos`
- **Clientes Jurídicos**: `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_juridicos`
- **Datos Davivienda**: `sb-ecosistemaanalitico-lago.davivienda.t_pj_datos_basicos`

## Lógica de Consulta

### 1. Declaración de Variables
Define `periodo_max_primas` como la fecha máxima de proceso disponible en la tabla de pólizas.

### 2. Base Principal: Clientes Vigentes
Extrae clientes jurídicos con:
- Pólizas vigentes y activas
- Tipos documento NT (Nacional Tercero) o NE (Nacional Empresa)

### 3. Normalización Davivienda
Recupera información de Davivienda normalizada a 9 dígitos.

### 4. Filtrado de Faltantes
**Criterio clave** (líneas 86-87):
```sql
WHERE b.CODIGO_CIIU IS NULL
   OR b.ACTIVIDAD_ECONOMICA IS NULL
```
Solo retorna clientes que tienen **al menos uno de estos campos nulo** en ARIS.

### 5. Deduplicación y Ordenamiento
- ROW_NUMBER() por KEY_ID para 1 registro por cliente
- Prioriza registros con datos de Davivienda disponibles
- Orden secundario por código descendente

## Campos de Salida

| Campo | Descripción |
|-------|-------------|
| `TIPO_DOCUMENTO` | Tipo de identificación |
| `KEY_ID` | NIT del cliente |
| `NOMBRE` | Nombre del cliente |
| `CODIGO_CIIU` | Código CIIU (ARIS) - **POTENCIALMENTE NULO** |
| `ACTIVIDAD_ECONOMICA` | Descripción actividad (ARIS) - **POTENCIALMENTE NULO** |
| `COD_TIPO_IDENTIFICACION` | Código tipo ID (Davivienda) |
| `TIPO_IDENTIFICACION` | Descripción tipo ID (Davivienda) |
| `ACTIVIDAD_ECONOMICA_DAV` | Descripción actividad (Davivienda) - candidato para llenar vacío |
| `COD_ACTIVIDAD_ECONOMICA` | Código actividad (Davivienda) - candidato para llenar vacío |

## Casos de Uso
- **Auditoría de integridad**: Detectar registros incompletos
- **Plan de remedición**: Priorizar qué clientes necesitan completar información
- **Enriquecimiento**: Usar datos de Davivienda para llenar campos faltantes
- **Control de calidad**: Monitoreo mensual de gaps de datos

## Diferencias con Archivo A
- **Archivo A**: Todos los clientes vigentes (completos o incompletos)
- **Archivo B**: **SOLO** clientes con campos faltantes (filtro WHERE)

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
    c.CODIGO_CIIU,
    c.ACTIVIDAD_ECONOMICA
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
    ACTIVIDAD_ECONOMICA AS ACTIVIDAD_ECONOMICA_DAV,
    COD_ACTIVIDAD_ECONOMICA
  FROM `sb-ecosistemaanalitico-lago.davivienda.t_pj_datos_basicos`
)

-- ============================================================
-- 5. RESULTADO FINAL (1 REGISTRO POR NIT)
-- ============================================================
SELECT
  TIPO_DOCUMENTO,
  KEY_ID,
  NOMBRE,
  CODIGO_CIIU,
  ACTIVIDAD_ECONOMICA,
  COD_TIPO_IDENTIFICACION,
  TIPO_IDENTIFICACION,
  ACTIVIDAD_ECONOMICA_DAV,
  COD_ACTIVIDAD_ECONOMICA
FROM (
  SELECT
    b.TIPO_DOCUMENTO,
    b.KEY_ID,
    b.NOMBRE,
    b.CODIGO_CIIU,
    b.ACTIVIDAD_ECONOMICA,
    d.COD_TIPO_IDENTIFICACION,
    d.TIPO_IDENTIFICACION,
    d.ACTIVIDAD_ECONOMICA_DAV,
    d.COD_ACTIVIDAD_ECONOMICA,

    -- Control de duplicados por NIT
    ROW_NUMBER() OVER (
      PARTITION BY b.KEY_ID 
      ORDER BY 
        -- prioriza registros con info de Davivienda
        CASE WHEN d.ACTIVIDAD_ECONOMICA_DAV IS NOT NULL THEN 1 ELSE 2 END,
        d.COD_ACTIVIDAD_ECONOMICA DESC
    ) AS rn

  FROM base b
  LEFT JOIN pj_davivienda d
    ON b.KEY_ID_SIN_DV = d.KEY_ID_SIN_DV
  -- FILTRO CLAVE: Solo clientes con datos incompletos
  WHERE b.CODIGO_CIIU IS NULL
     OR b.ACTIVIDAD_ECONOMICA IS NULL
)
WHERE rn = 1;
```

---

**Categoría**: Calidad de Datos - Auditoría de Integridad  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Tipo de Consulta**: Identificación de valores faltantes  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29