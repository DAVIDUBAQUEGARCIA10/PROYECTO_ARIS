# Calidad de Datos: Nombre y Terceros Completos

## Descripción
Auditoría de completitud de nombre y terceros relacionados. Verifica que clientes tengan nombre poblado y sus terceros relacionados registrados y vigentes.

---

## Código SQL - Oracle

```sql
SELECT 
  j.numero_documento,
  j.tipdoc_codigo,
  j.nombre,
  COUNT(t.numero_id_tercero) AS cantidad_terceros_relacionados,
  SUM(CASE WHEN t.estado = 'ACTIVO' THEN 1 ELSE 0 END) AS terceros_activos,
  CASE 
    WHEN j.nombre IS NULL OR TRIM(j.nombre) = '' THEN 'NOMBRE_FALTANTE'
    WHEN COUNT(t.numero_id_tercero) = 0 THEN 'SIN_TERCEROS'
    WHEN SUM(CASE WHEN t.estado = 'ACTIVO' THEN 1 ELSE 0 END) = 0 THEN 'TERCEROS_INACTIVOS'
    ELSE 'COMPLETO'
  END AS evaluacion
FROM juridicos j
LEFT JOIN terceros_relacionados t ON t.numero_id_cliente = j.numero_documento
WHERE j.estado = 'ACTIVO'
GROUP BY j.numero_documento, j.tipdoc_codigo, j.nombre;
```

---

**Categoría**: Calidad de Datos - Terceros Relacionados  
**Base de Datos**: Oracle  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29