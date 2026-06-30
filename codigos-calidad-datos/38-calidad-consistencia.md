# Calidad de Datos: Consistencia

## Descripción
Validación de consistencia entre tablas. Verifica que representantes legales de PJ sean PN válidas, que pólizas tengan clientes activos, que correos sean consistentes entre naturales y jurídicos.

---

## Código SQL - Oracle

```sql
WITH CONSISTENCIA_REPRESENTANTE AS (
  SELECT j.numero_documento, j.tipdoc_codigo,
    CASE
      WHEN j.numero_documento_representante IS NOT NULL 
        AND EXISTS (SELECT 1 FROM naturales n WHERE n.numero_documento = j.numero_documento_representante)
      THEN 'CONSISTENTE'
      ELSE 'INCONSISTENTE'
    END AS consistencia_rep
  FROM juridicos j
),

CONSISTENCIA_POLIZA AS (
  SELECT a.COD_CIA, a.tdoc_tercero, a.nro_documto,
    CASE
      WHEN EXISTS (SELECT 1 FROM juridicos j 
        WHERE j.tipdoc_codigo = a.tdoc_tercero AND j.numero_documento = a.nro_documto)
      THEN 'CONSISTENTE'
      ELSE 'INCONSISTENTE'
    END AS consistencia_poliza
  FROM A2000030 a
  WHERE a.FECHA_VENC_POL >= SYSDATE
),

CONSISTENCIA_CORREO AS (
  SELECT numero_documento, tipdoc_codigo,
    CASE
      WHEN correo_unificado IS NOT NULL
        AND correo_unificado = LAG(correo_unificado) 
          OVER (PARTITION BY numero_documento ORDER BY tipdoc_codigo)
      THEN 'CONSISTENTE'
      ELSE 'VARIABLE'
    END AS consistencia_correo
  FROM CORREOS_UNIFICADOS
)

SELECT * FROM CONSISTENCIA_POLIZA WHERE consistencia_poliza = 'INCONSISTENTE';
```

---

**Categoría**: Calidad de Datos - Consistencia  
**Base de Datos**: Oracle  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29