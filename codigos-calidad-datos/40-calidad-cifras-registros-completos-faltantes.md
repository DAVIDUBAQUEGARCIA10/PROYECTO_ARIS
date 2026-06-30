# Calidad de Datos: Cifras - Total Registros, Completos, Faltantes e Inválidos

## Descripción
Reporte ejecutivo de calidad de datos: cuenta total de registros, cuántos están completos, cuántos tienen faltantes y cuántos tienen datos inválidos.

---

## Código SQL - Oracle

```sql
WITH ESTADISTICAS AS (
  SELECT 
    COUNT(DISTINCT CONCAT(tipdoc_codigo, '-', numero_documento)) AS total_registros,
    SUM(CASE WHEN correo_unificado IS NOT NULL AND celular_unificado IS NOT NULL 
      THEN 1 ELSE 0 END) AS registros_completos,
    SUM(CASE WHEN correo_unificado IS NULL OR celular_unificado IS NULL 
      THEN 1 ELSE 0 END) AS registros_faltantes,
    SUM(CASE WHEN (correo_unificado NOT LIKE '%@%' OR LENGTH(celular_unificado) != 10) 
      THEN 1 ELSE 0 END) AS registros_invalidos
  FROM (
    SELECT * FROM CORREOS_UNIFICADOS FULL OUTER JOIN CELULAR_UNIFICADO USING (numero_documento, tipdoc_codigo)
  )
)

SELECT 
  total_registros,
  registros_completos,
  registros_faltantes,
  registros_invalidos,
  ROUND((registros_completos / total_registros) * 100, 2) AS porcentaje_completos
FROM ESTADISTICAS;
```

---

**Categoría**: Calidad de Datos - Reportería  
**Base de Datos**: Oracle  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29