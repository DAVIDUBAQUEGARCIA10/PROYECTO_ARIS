# Calidad de Datos: Activos y Pasivos - Personas Naturales

## Descripción
Análisis de activos y pasivos de personas naturales desde tabla de trazabilidad con comparación entre ARIS y Davivienda.

---

## Código SQL

```sql
WITH base AS (
  SELECT DISTINCT TIPO_DOCUMENTO, KEY_ID
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_trazabilidad_formatos_servicios_marcas`
  WHERE TIPO_DOCUMENTO NOT IN ('NT', 'NE')
),

pn_aris AS (
  SELECT TIPO_DOCUMENTO, KEY_ID, VALOR_ACTIVO, VALOR_PASIVO
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_naturales`
),

pn_davivienda AS (
  SELECT KEY_ID, VALOR_ACTIVO AS VALOR_ACTIVO_DAV, VALOR_PASIVO AS VALOR_PASIVO_DAV
  FROM `sb-ecosistemaanalitico-lago.davivienda.t_pn_datos_basicos`
)

SELECT b.TIPO_DOCUMENTO, b.KEY_ID, a.VALOR_ACTIVO, a.VALOR_PASIVO,
  d.VALOR_ACTIVO_DAV, d.VALOR_PASIVO_DAV
FROM base b
LEFT JOIN pn_aris a ON b.TIPO_DOCUMENTO = a.TIPO_DOCUMENTO AND b.KEY_ID = a.KEY_ID
LEFT JOIN pn_davivienda d ON b.KEY_ID = d.KEY_ID;
```

---

**Categoría**: Calidad de Datos - Activos/Pasivos PN  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29