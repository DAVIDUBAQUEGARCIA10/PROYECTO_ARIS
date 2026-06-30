# Calidad de Datos: Fecha de Nacimiento - Personas Naturales Base

## Descripción
Análisis de fechas de nacimiento de personas naturales en revisoria fiscal. Detecta personas naturales con registros en revisoria fiscal. Incluye validación de fechas (1901-actual) para descartar datos inválidos.

## Fuentes de Datos
- `sb-ecosistemaanalitico-lago.seguros_bolivar.t_trazabilidad_formatos_servicios_marcas` (Base principal)
- `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_naturales` (Fechas ARIS)
- `sb-ecosistemaanalitico-lago.davivienda.t_pn_datos_basicos` (Fechas Davivienda)

## Lógica de Consulta

Base: Tipo documento ≠ NT/NE (personas naturales).

Validación de fechas: Descartar:
- Fechas < 1901-01-01 (demasiado antiguas)
- Fechas > Fecha actual (futuras)

Resultado: Compara fechas entre ARIS y Davivienda.

---

## Código SQL

```sql
WITH base AS (
  SELECT DISTINCT
    TIPO_DOCUMENTO,
    KEY_ID
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_trazabilidad_formatos_servicios_marcas`
  WHERE TIPO_DOCUMENTO NOT IN ('NT', 'NE')
),

clientes_naturales AS (
  SELECT
    TIPO_DOCUMENTO,
    KEY_ID,

    CASE
      WHEN SAFE_CAST(SUBSTR(CAST(FEC_NACIMIENTO AS STRING), 1, 10) AS DATE) < DATE('1901-01-01')
        OR SAFE_CAST(SUBSTR(CAST(FEC_NACIMIENTO AS STRING), 1, 10) AS DATE) > CURRENT_DATE()
      THEN NULL
      ELSE SAFE_CAST(SUBSTR(CAST(FEC_NACIMIENTO AS STRING), 1, 10) AS DATE)
    END AS FEC_NACIMIENTO

  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_naturales`
),

pn_davivienda AS (
  SELECT
    KEY_ID,

    CASE
      WHEN SAFE_CAST(SUBSTR(CAST(FECHA_NACIMIENTO AS STRING), 1, 10) AS DATE) < DATE('1901-01-01')
        OR SAFE_CAST(SUBSTR(CAST(FECHA_NACIMIENTO AS STRING), 1, 10) AS DATE) > CURRENT_DATE()
      THEN NULL
      ELSE SAFE_CAST(SUBSTR(CAST(FECHA_NACIMIENTO AS STRING), 1, 10) AS DATE)
    END AS FECHA_NACIMIENTO_DAV

  FROM `sb-ecosistemaanalitico-lago.davivienda.t_pn_datos_basicos`
)

SELECT
  b.TIPO_DOCUMENTO,
  b.KEY_ID,
  cn.FEC_NACIMIENTO,
  d.FECHA_NACIMIENTO_DAV
FROM base b
LEFT JOIN clientes_naturales cn
  ON b.TIPO_DOCUMENTO = cn.TIPO_DOCUMENTO
 AND b.KEY_ID = cn.KEY_ID
LEFT JOIN pn_davivienda d
  ON b.KEY_ID = d.KEY_ID;
```

---

**Categoría**: Calidad de Datos - Personas Naturales  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29