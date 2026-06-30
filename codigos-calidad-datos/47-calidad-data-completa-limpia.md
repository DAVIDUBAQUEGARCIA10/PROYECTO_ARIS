# Calidad de Datos: Data Completa y Limpia

## Descripción
Vista integral de datos limpios: clientes con información completa, validada y consistente. Excluye registros inactivos, duplicados o con inconsistencias.

---

## Código SQL - Oracle

```sql
WITH CLIENTES_LIMPIOS AS (
  SELECT 
    numero_documento,
    tipdoc_codigo,
    nombre,
    estado,
    correo_unificado,
    celular_unificado,
    COUNT(DISTINCT NUM_POL1) AS cantidad_polizas_vigentes,
    CASE 
      WHEN numero_documento IS NOT NULL
        AND tipdoc_codigo IS NOT NULL
        AND nombre IS NOT NULL
        AND estado = 'ACTIVO'
        AND correo_unificado IS NOT NULL
        AND celular_unificado IS NOT NULL
        AND COUNT(DISTINCT NUM_POL1) > 0
      THEN 'COMPLETO_LIMPIO'
      ELSE 'INCOMPLETO'
    END AS calidad
  FROM (
    SELECT j.numero_documento, j.tipdoc_codigo, j.nombre, j.estado,
      c.correo_unificado, ce.celular_unificado, p.NUM_POL1
    FROM juridicos j
    LEFT JOIN CORREOS_UNIFICADOS c USING (numero_documento, tipdoc_codigo)
    LEFT JOIN CELULAR_UNIFICADO ce USING (numero_documento, tipdoc_codigo)
    LEFT JOIN A2000030 p ON p.tdoc_tercero = j.tipdoc_codigo AND p.nro_documto = j.numero_documento
    WHERE j.estado = 'ACTIVO'
  )
  GROUP BY numero_documento, tipdoc_codigo, nombre, estado, correo_unificado, celular_unificado
)

SELECT * FROM CLIENTES_LIMPIOS WHERE calidad = 'COMPLETO_LIMPIO';
```

---

**Categoría**: Calidad de Datos - Data Limpia  
**Base de Datos**: Oracle  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29