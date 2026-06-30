# Calidad de Datos: Davivienda - Para Completar

## Descripción
Extrae datos de Davivienda para completar información faltante en ARIS. Base normalizada a 9 dígitos para matching con clientes de SB.

---

## Código SQL

```sql
WITH base AS (
  SELECT DISTINCT TIPO_DOCUMENTO, KEY_ID
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_trazabilidad_formatos_servicios_marcas`
  WHERE TIPO_DOCUMENTO IN ('NT', 'NE')
),

pj_davivienda AS (
  SELECT SUBSTR(CAST(KEY_ID AS STRING), 1, 9) AS KEY_ID_SIN_DV,
    COD_TIPO_IDENTIFICACION, TIPO_IDENTIFICACION,
    ACTIVIDAD_ECONOMICA, COD_ACTIVIDAD_ECONOMICA,
    FECHA_CONSTITUCION, FECHA_NACIMIENTO
  FROM `sb-ecosistemaanalitico-lago.davivienda.t_pj_datos_basicos`
)

SELECT b.TIPO_DOCUMENTO, SUBSTR(CAST(b.KEY_ID AS STRING), 1, 9) AS KEY_ID_SIN_DV,
  d.TIPO_IDENTIFICACION, d.ACTIVIDAD_ECONOMICA, d.COD_ACTIVIDAD_ECONOMICA,
  d.FECHA_CONSTITUCION
FROM base b
LEFT JOIN pj_davivienda d ON SUBSTR(CAST(b.KEY_ID AS STRING), 1, 9) = d.KEY_ID_SIN_DV;
```

---

**Categoría**: Calidad de Datos - Enriquecimiento Davivienda  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago.davivienda`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29