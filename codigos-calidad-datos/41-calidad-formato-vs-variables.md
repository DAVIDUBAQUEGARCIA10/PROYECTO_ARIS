# Calidad de Datos: Formato vs Variables Sin Análisis

## Descripción
Análisis de estructura de datos: compara formato de datos guardados (estructura) contra variables sin procesamiento analítico (raw data).

---

## Código SQL - Oracle

```sql
SELECT 
  'FORMATO_CORREO' AS tipo_dato,
  COUNT(*) AS cantidad,
  SUM(CASE WHEN descripcion LIKE '%@%' AND descripcion LIKE '%.%' THEN 1 ELSE 0 END) AS con_formato_valido,
  SUM(CASE WHEN descripcion IS NULL OR TRIM(descripcion) = '' THEN 1 ELSE 0 END) AS sin_formato
FROM medios_comunicacion
WHERE tipmedcom_codigo IN (SELECT secuencia FROM tipos_medios_comunicacion WHERE descripcion LIKE '%EMAIL%')

UNION ALL

SELECT 
  'FORMATO_CELULAR',
  COUNT(*),
  SUM(CASE WHEN REGEXP_LIKE(TRIM(descripcion), '^[0-9]{10}$') THEN 1 ELSE 0 END),
  SUM(CASE WHEN descripcion IS NULL OR NOT REGEXP_LIKE(TRIM(descripcion), '^[0-9]{10}$') THEN 1 ELSE 0 END)
FROM medios_comunicacion
WHERE tipmedcom_codigo IN (SELECT secuencia FROM tipos_medios_comunicacion WHERE descripcion LIKE '%CELULAR%');
```

---

**Categoría**: Calidad de Datos - Análisis de Estructura  
**Base de Datos**: Oracle  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29