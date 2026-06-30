# Calidad de Datos: Ingresos y Egresos - Davivienda

## Descripción
Extrae ingresos y egresos de personas jurídicas desde Davivienda para comparación y validación con ARIS.

---

## Código SQL

```sql
SELECT DISTINCT TIPO_DOCUMENTO, KEY_ID,
  VALOR_INGRESOS AS VALOR_INGRESOS_DAV,
  VALOR_EGRESOS AS VALOR_EGRESOS_DAV
FROM `sb-ecosistemaanalitico-lago.davivienda.t_pj_datos_basicos`;
```

---

**Categoría**: Calidad de Datos - Ingresos/Egresos Davivienda  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago.davivienda`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29