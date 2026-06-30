# Calidad de Datos: Variable Financiera - Activo y Pasivo Davivienda

## Descripción
Extrae variables financieras (activo y pasivo) desde Davivienda para comparación y enriquecimiento de datos ARIS.

---

## Código SQL

```sql
SELECT DISTINCT TIPO_DOCUMENTO, KEY_ID,
  VALOR_ACTIVO AS VALOR_ACTIVO_DAV,
  VALOR_PASIVO AS VALOR_PASIVO_DAV
FROM `sb-ecosistemaanalitico-lago.davivienda.t_pj_datos_basicos`;
```

---

**Categoría**: Calidad de Datos - Variables Financieras Davivienda  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago.davivienda`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29