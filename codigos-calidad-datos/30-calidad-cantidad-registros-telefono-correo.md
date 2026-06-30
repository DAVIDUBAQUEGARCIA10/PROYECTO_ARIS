# Calidad de Datos: Cantidad de Registros - Teléfono y Correo

## Descripción
Análisis de cobertura de contacto: cantidad de registros de teléfono y correo por cliente. Auditoría de integridad de datos de contacto.

---

## Código SQL

```sql
WITH CORREOS_UNIFICADOS AS (
  SELECT n.numero_documento, n.tipdoc_codigo AS tipo_documento,
    COUNT(DISTINCT c.numero_correo) AS cantidad_correos
  FROM numeros_telefono n
  LEFT JOIN correos c ON n.numero_documento = c.numero_documento
    AND n.tipdoc_codigo = c.tipdoc_codigo
  GROUP BY n.numero_documento, n.tipdoc_codigo
),

TELEFONOS_UNIFICADOS AS (
  SELECT numero_documento, tipdoc_codigo AS tipo_documento,
    COUNT(DISTINCT numero_telefono) AS cantidad_telefonos
  FROM numeros_telefono
  GROUP BY numero_documento, tipdoc_codigo
)

SELECT c.tipo_documento, c.numero_documento,
  c.cantidad_correos, t.cantidad_telefonos
FROM CORREOS_UNIFICADOS c
FULL OUTER JOIN TELEFONOS_UNIFICADOS t
  ON c.numero_documento = t.numero_documento
  AND c.tipo_documento = t.tipo_documento;
```

---

**Categoría**: Calidad de Datos - Contacto  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29