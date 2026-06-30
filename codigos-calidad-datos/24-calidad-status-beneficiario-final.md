# Calidad de Datos: Status Beneficiario Final - Comparativa

## Descripción
Análisis comparativo del status real de beneficiarios finales entre ARIS, Terceros y Davivienda. Cálculo de sumatoria de participación accionaria y conteo de accionistas.

---

## Código SQL

```sql
WITH base AS (
    SELECT DISTINCT TIPO_DOCUMENTO, KEY_ID
    FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_trazabilidad_formatos_servicios_marcas`
    WHERE TIPO_DOCUMENTO IN ('NT', 'NE')
),

beneficiarios_bolivar AS (
    SELECT TIPO_DOCUMENTO, KEY_ID,
      SUM(PORCENTAJE_PARTICIPACION) AS SUMATORIA_COMPOSICION_ACCIONARIA_BOLIVAR,
      COUNT(DISTINCT CONCAT(CAST(CODIGO_TIPO_DOCUMENTO_CONTACTO AS STRING), '-',
        CAST(KEY_ID_CONTACTO AS STRING))) AS CANTIDAD_ACCIONISTAS_BOLIVAR
    FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_beneficiario_final`
    WHERE PORCENTAJE_PARTICIPACION > 0 AND MARCA_REGISTRO_ACTIVO = 'A'
    GROUP BY TIPO_DOCUMENTO, KEY_ID
),

beneficiarios_davivienda AS (
    SELECT a.TIPO_DOCUMENTO_CLIENTE, a.KEY_ID_CLIENTE,
      SUM(a.PORCENTAJE_PARTICIPACION) AS SUMATORIA_COMPOSICION_ACCIONARIA_DAVIVIENDA,
      COUNT(DISTINCT CONCAT(a.TIPO_DOCUMENTO_RELACIONADO, '-', a.KEY_ID_RELACIONADO)) 
        AS CANTIDAD_ACCIONISTAS_DAVIVIENDA
    FROM `sb-ecosistemaanalitico-lago.davivienda.t_individuos_relacionados_pn_pj` a
    WHERE ESTADO_DATO = 'VIGENTE' AND PORCENTAJE_PARTICIPACION > 0
    GROUP BY a.TIPO_DOCUMENTO_CLIENTE, a.KEY_ID_CLIENTE
)

SELECT b.TIPO_DOCUMENTO, b.KEY_ID, 
  bb.SUMATORIA_COMPOSICION_ACCIONARIA_BOLIVAR, bb.CANTIDAD_ACCIONISTAS_BOLIVAR,
  bd.SUMATORIA_COMPOSICION_ACCIONARIA_DAVIVIENDA, bd.CANTIDAD_ACCIONISTAS_DAVIVIENDA
FROM base b
LEFT JOIN beneficiarios_bolivar bb ON b.TIPO_DOCUMENTO = bb.TIPO_DOCUMENTO AND b.KEY_ID = bb.KEY_ID
LEFT JOIN beneficiarios_davivienda bd ON b.TIPO_DOCUMENTO = bd.TIPO_DOCUMENTO_CLIENTE 
  AND b.KEY_ID = bd.KEY_ID_CLIENTE;
```

---

**Categoría**: Calidad de Datos - Beneficiarios Finales  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29