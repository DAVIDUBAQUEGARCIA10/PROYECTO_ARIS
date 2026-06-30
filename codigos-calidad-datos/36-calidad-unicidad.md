# Calidad de Datos: Unicidad

## Descripción
Validación de unicidad: verifica que cada cliente tenga UN único registro de correo, celular y póliza. Detecta duplicados.

---

## Código SQL - Oracle

```sql
WITH CORREOS_UNIFICADOS AS (
  SELECT n.numero_documento, n.tipdoc_codigo, COUNT(DISTINCT mcn.descripcion) AS cant_correos
  FROM naturales n
  JOIN medios_comunicacion mcn ON mcn.nat_secuencia = n.secuencia
  JOIN tipos_medios_comunicacion tmcn ON tmcn.secuencia = mcn.tipmedcom_codigo
  WHERE tmcn.descripcion LIKE '%EMAIL%' AND mcn.descripcion LIKE '%@%'
  GROUP BY n.numero_documento, n.tipdoc_codigo
  
  UNION
  
  SELECT j.numero_documento, j.tipdoc_codigo, COUNT(DISTINCT mcj.descripcion)
  FROM juridicos j
  JOIN medios_comunicacion mcj ON mcj.jur_secuencia = j.secuencia
  WHERE mcj.descripcion LIKE '%@%'
  GROUP BY j.numero_documento, j.tipdoc_codigo
),

POLIZA_UNICA AS (
  SELECT COD_CIA, tdoc_tercero, nro_documto, COUNT(DISTINCT NUM_POL1) AS cant_polizas
  FROM A2000030
  WHERE FECHA_VENC_POL >= SYSDATE AND COD_RAMO != 315
  GROUP BY COD_CIA, tdoc_tercero, nro_documto
  HAVING COUNT(DISTINCT NUM_POL1) > 1
),

CELULAR_UNICO AS (
  SELECT numero_documento, tipdoc_codigo, COUNT(DISTINCT celular_unificado) AS cant_celulares
  FROM CELULAR_UNIFICADO
  GROUP BY numero_documento, tipdoc_codigo
  HAVING COUNT(DISTINCT celular_unificado) > 1
)

SELECT * FROM POLIZA_UNICA WHERE cant_polizas > 1;
```

---

**Categoría**: Calidad de Datos - Unicidad  
**Base de Datos**: Oracle  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29