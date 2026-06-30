# LAFT_laft_comportamiento_score_riesgo_incremental

## Descripcion
Script o consulta del proyecto ARIS.

## Tipo
Archivo: 
Carpeta: Score de Terceros

## Contenido

```
CREATE OR REPLACE TABLE sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.laft_comportamiento_score_riesgo_incremental AS 
SELECT
    CODIGO_COMPANIA,
    Nivel_de_Riesgo AS NIVEL_DE_RIESGO_POSTERIOS,
    FECHA_CALIFICACION_POSTERIOS,
    COUNT(*) AS cantidad_registros
FROM 
    `sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.Score_de_terceros_Incremento_Riesgo_prod`
GROUP BY
    CODIGO_COMPANIA,
    NIVEL_DE_RIESGO_POSTERIOS,
    FECHA_CALIFICACION_POSTERIOS
ORDER BY
    FECHA_CALIFICACION_POSTERIOS DESC,
    CODIGO_COMPANIA,
    NIVEL_DE_RIESGO_POSTERIOS;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
Archivo Original: LAFT_laft_comportamiento_score_riesgo_incremental
