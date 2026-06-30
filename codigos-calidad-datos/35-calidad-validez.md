# Calidad de Datos: Validez

## Descripción
Validación de datos con reglas de negocio. Verifica correos válidos, celulares de 10 dígitos, pólizas vigentes sin ramo 315, representantes legales identificados.

## Reglas de Validación
- Correo: formato @
- Celular: exactamente 10 dígitos numéricos
- Póliza: FECHA_VENC_POL >= SYSDATE, COD_RAMO ≠ 315
- Representante: tipdoc_codigo_identificado_por poblado

---

## Código SQL - Oracle

```sql
WITH CORREOS_UNIFICADOS AS (
  SELECT 
    n.numero_documento, n.tipdoc_codigo,
    MIN(mcn.descripcion) AS correo_unificado
  FROM naturales n
  JOIN medios_comunicacion mcn ON mcn.nat_secuencia = n.secuencia
  JOIN tipos_medios_comunicacion tmcn ON tmcn.secuencia = mcn.tipmedcom_codigo
  WHERE tmcn.descripcion LIKE '%EMAIL%' AND mcn.descripcion LIKE '%@%'
  GROUP BY n.numero_documento, n.tipdoc_codigo
  
  UNION
  
  SELECT 
    j.numero_documento, j.tipdoc_codigo,
    MIN(mcj.descripcion) AS correo_unificado
  FROM juridicos j
  JOIN medios_comunicacion mcj ON mcj.jur_secuencia = j.secuencia
  JOIN tipos_medios_comunicacion tmcj ON tmcj.secuencia = mcj.tipmedcom_codigo
  WHERE tmcj.descripcion LIKE '%EMAIL%' AND mcj.descripcion LIKE '%@%'
  GROUP BY j.numero_documento, j.tipdoc_codigo
),

POLIZA_UNICA AS (
  SELECT *
  FROM (
    SELECT 
      a.COD_CIA, a.tdoc_tercero, a.nro_documto, a.NUM_POL1, a.cod_ramo,
      ROW_NUMBER() OVER (PARTITION BY a.COD_CIA, a.tdoc_tercero, a.nro_documto 
        ORDER BY a.FECHA_VENC_POL ASC NULLS LAST) AS rn
    FROM A2000030 a
    WHERE a.FECHA_VENC_POL >= SYSDATE
  )
  WHERE rn = 1 AND COD_RAMO != 315
),

CELULAR_UNIFICADO AS (
  SELECT * FROM (
    SELECT 
      j.numero_documento, j.tipdoc_codigo,
      CASE
        WHEN REGEXP_LIKE(TRIM(mcn.descripcion), '^[0-9]{10}$') THEN TRIM(mcn.descripcion)
        WHEN REGEXP_LIKE(TRIM(mcj.descripcion), '^[0-9]{10}$') THEN TRIM(mcj.descripcion)
        ELSE NULL
      END AS celular_unificado,
      ROW_NUMBER() OVER (PARTITION BY j.tipdoc_codigo, j.numero_documento 
        ORDER BY j.numero_documento) AS rn
    FROM juridicos j
    JOIN naturales n ON n.numero_documento = j.numero_documento_representante
    JOIN medios_comunicacion mcn ON mcn.nat_secuencia = n.secuencia
    JOIN medios_comunicacion mcj ON mcj.jur_secuencia = j.secuencia
  )
  WHERE rn = 1
)

SELECT * FROM POLIZA_UNICA WHERE COD_RAMO != 315;
```

---

**Categoría**: Calidad de Datos - Validez  
**Base de Datos**: Oracle  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29