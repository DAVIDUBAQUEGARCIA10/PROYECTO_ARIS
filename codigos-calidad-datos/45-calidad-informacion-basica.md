# Calidad de Datos: Información Básica

## Descripción
Auditoría de información básica de clientes: nombre, tipo documento, número documento, estado, fecha de registro. Identifica campos críticos faltantes o inválidos.

---

## Código SQL - Oracle

```sql
SELECT 
  numero_documento,
  tipdoc_codigo,
  nombre,
  estado,
  fecha_registro,
  CASE 
    WHEN numero_documento IS NULL THEN 'FALTA_NUMERO'
    WHEN tipdoc_codigo IS NULL THEN 'FALTA_TIPO_DOC'
    WHEN nombre IS NULL OR TRIM(nombre) = '' THEN 'FALTA_NOMBRE'
    WHEN estado NOT IN ('ACTIVO', 'INACTIVO', 'CANCELADO') THEN 'ESTADO_INVALIDO'
    ELSE 'COMPLETO'
  END AS evaluacion_complitud
FROM (
  SELECT numero_documento, tipdoc_codigo, nombre, estado, fecha_registro FROM naturales
  UNION ALL
  SELECT numero_documento, tipdoc_codigo, nombre, estado, fecha_registro FROM juridicos
)
WHERE estado = 'ACTIVO';
```

---

**Categoría**: Calidad de Datos - Información Básica  
**Base de Datos**: Oracle  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29