# Calidad de Datos: Actividad Económica - Clientes Vigentes

## Descripción
Extrae actividad económica de clientes jurídicos (NT/NE) con productos vigentes en Seguros Bolívar, normalizando la información con datos de Davivienda. Selecciona un único registro por NIT, priorizando la información disponible en Davivienda cuando existe.

## Fuentes de Datos
- **Base Principal**: `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos`
- **Clientes Jurídicos**: `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_juridicos`
- **Datos Davivienda**: `sb-ecosistemaanalitico-lago.davivienda.t_pj_datos_basicos`

## Lógica de Consulta

### 1. Declaración de Variables
Define `periodo_max_primas` como la fecha máxima de proceso disponible en la tabla de pólizas, asegurando que se usa siempre la información más reciente.

### 2. Base Principal: Clientes con Productos Vigentes
Extrae clientes jurídicos que cumplen criterios:
- Pólizas no anuladas (`MCA_ANU_POLIZA != "S"`)
- Fecha de proceso igual a la máxima registrada
- Endosos vigentes a la fecha actual
- Pólizas vigentes a la fecha actual
- Tipos de documento: NT (Nacional Tercero) o NE (Nacional Empresa)

### 3. Normalización de Datos Davivienda
Recupera información de Davivienda normalizada:
- KEY_ID reducido a 9 dígitos (sin dígito verificador)
- Actividad económica y código de actividad

### 4. Unión y Deduplicación
- **JOIN**: LEFT JOIN entre base ARIS y datos de Davivienda
- **Deduplicación**: ROW_NUMBER() por KEY_ID (NIT) para seleccionar un registro por cliente
- **Priorización**: Ordena por presencia de datos Davivienda (1 = con datos, 2 = sin datos)
- **Orden secundario**: COD_ACTIVIDAD_ECONOMICA DESC (códigos más específicos primero)

## Campos de Salida

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `TIPO_DOCUMENTO` | STRING | Tipo de identificación (NT/NE) |
| `KEY_ID` | INT64 | Número de identificación (NIT) con dígito verificador |
| `NOMBRE` | STRING | Nombre del cliente jurídico |
| `CODIGO_CIIU` | STRING | Código CIIU según Seguros Bolívar |
| `ACTIVIDAD_ECONOMICA` | STRING | Descripción de actividad económica (Seguros Bolívar) |
| `COD_TIPO_IDENTIFICACION` | STRING | Código tipo ID según Davivienda |
| `TIPO_IDENTIFICACION` | STRING | Descripción tipo ID (Davivienda) |
| `ACTIVIDAD_ECONOMICA_DAV` | STRING | Descripción actividad según Davivienda |
| `COD_ACTIVIDAD_ECONOMICA` | STRING | Código numérico de actividad (Davivienda) |

## Casos de Uso
- **Auditoría de datos**: Validar que clientes vigentes tengan actividad económica registrada
- **Enriquecimiento de datos**: Completar campos faltantes con datos de Davivienda
- **Análisis de negocios**: Segmentar clientes por tipo de actividad económica
- **Control de calidad**: Identificar inconsistencias entre ARIS y Davivienda

## Criterios de Filtrado
- Solo clientes jurídicos activos
- Solo pólizas vigentes
- Fecha de proceso coincide con la máxima disponible
- Excluye pólizas anuladas

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
)
WHERE rn = 1;
```

---

**Categoría**: Calidad de Datos - Validación de Datos Maestros  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Tipo de Consulta**: Normalización y enriquecimiento de datos  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29