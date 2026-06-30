# Calidad de Datos: Fecha de Nacimiento PN - Campos Faltantes

## Descripción
Identifica personas naturales en revisoria fiscal con **FEC_NACIMIENTO NULL en ARIS**. Campos faltantes indican falta de información personal completa.

## Diferencia con Archivo Base
- **Base**: Todas las personas naturales
- **Faltantes**: SOLO PN sin fecha de nacimiento registrada

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
  ON b.KEY_ID = d.KEY_ID
WHERE FEC_NACIMIENTO IS NULL AND FECHA_NACIMIENTO_DAV IS NULL;
```

---

**Categoría**: Calidad de Datos - Auditoría Integridad PN  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29