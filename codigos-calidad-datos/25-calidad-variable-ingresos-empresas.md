# Calidad de Datos: Variable de Ingresos - Empresas

## Descripción
Extrae variable de ingresos de personas jurídicas. Filtra valores significativos (>= 1000) de ingreso reportado.

---

## Código SQL

```sql
SELECT DISTINCT TIPO_DOCUMENTO, KEY_ID,
  CASE WHEN VALOR_INGRESOS < 1000 THEN NULL ELSE VALOR_INGRESOS END AS VALOR_INGRESOS
FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_juridicos`
WHERE TIPO_DOCUMENTO IN ('NT', 'NE');
```

---

**Categoría**: Calidad de Datos - Variables Financieras  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29