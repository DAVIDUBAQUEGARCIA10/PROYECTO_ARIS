# Calidad de Datos: Actividad Económica - Faltantes con Datos en Davivienda

## Descripción
Identifica clientes jurídicos con productos vigentes que tienen campos faltantes en ARIS (CODIGO_CIIU y/o ACTIVIDAD_ECONOMICA) **PERO que SÍ tienen información disponible en Davivienda**. Este es un subconjunto del archivo B con mayor prioridad para remedición, ya que los datos están disponibles para ser completados.

## Fuentes de Datos
- **Base Principal**: `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos`
- **Clientes Jurídicos**: `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_juridicos`
- **Datos Davivienda**: `sb-ecosistemaanalitico-lago.davivienda.t_pj_datos_basicos`

## Lógica de Consulta

### 1-4. Pasos Iniciales
Idénticos al archivo B: extrae clientes vigentes y normaliza datos de Davivienda.

### 5. Filtrado Dual (Criterio Clave)
**Primera condición** (líneas 86-88):
```sql
WHERE (b.CODIGO_CIIU IS NULL OR b.ACTIVIDAD_ECONOMICA IS NULL)
```
Cliente tiene datos faltantes en ARIS.

**Segunda condición** (líneas 87-88):
```sql
AND (d.COD_ACTIVIDAD_ECONOMICA IS NOT NULL OR d.ACTIVIDAD_ECONOMICA_DAV IS NOT NULL)
```
Cliente **SÍ** tiene datos disponibles en Davivienda.

**Resultado**: Solo clientes "remedibles" - tienen datos en Davivienda listos para usar.

## Campos de Salida

| Campo | Descripción |
|-------|-------------|
| `CODIGO_CIIU` | NULL en ARIS → Se puede llenar desde `COD_ACTIVIDAD_ECONOMICA` |
| `ACTIVIDAD_ECONOMICA` | NULL en ARIS → Se puede llenar desde `ACTIVIDAD_ECONOMICA_DAV` |
| `COD_ACTIVIDAD_ECONOMICA` | Fuente confiable de Davivienda (valor a usar) |
| `ACTIVIDAD_ECONOMICA_DAV` | Fuente confiable de Davivienda (valor a usar) |

## Casos de Uso
- **Plan de remedición prioritario**: Enfocarse primero en estos registros (tienen solución a mano)
- **Data governance**: Automatizar población de campos faltantes con Davivienda
- **Validación de calidad**: Confirmar que Davivienda tiene cobertura completa vs ARIS gaps
- **Business intelligence**: Segmentación más precisa donde haya datos

## Diferencias de Archivos A, B, C

| Archivo | Filtro | Objetivo |
|---------|--------|----------|
| **A** | Todos los clientes vigentes | Universo completo de clientes |
| **B** | Clientes con faltantes en ARIS | Auditoría de gaps |
| **C** | Clientes con faltantes EN ARIS Y datos EN Davivienda | **Prioridad 1 para remedición** |

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
  -- FILTRO CLAVE: Faltantes en ARIS Y datos disponibles en Davivienda
  WHERE (b.CODIGO_CIIU IS NULL
     OR b.ACTIVIDAD_ECONOMICA IS NULL)
    AND (d.COD_ACTIVIDAD_ECONOMICA IS NOT NULL
      OR d.ACTIVIDAD_ECONOMICA_DAV IS NOT NULL) 
)
WHERE rn = 1;
```

---

**Categoría**: Calidad de Datos - Plan de Remedición  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Tipo de Consulta**: Identificación de gaps remediables  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29  
**Prioridad**: ALTA - Estos registros tienen solución inmediata disponible