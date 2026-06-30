# LAFT_laft_comportamiento_score_riesgo

## Descripcion
Script o consulta del proyecto ARIS.

## Tipo
Archivo: 
Carpeta: Score de Terceros

## Contenido

```
CREATE OR REPLACE TABLE sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.laft_comportamiento_score_riesgo as
SELECT
    CODIGO_COMPANIA,
    Nivel_de_Riesgo,
    Fecha_Calificacion AS Fecha_Calificacion,
    COUNT(*) AS cantidad_registros
FROM 
    `sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.score_acumulado_en_el_tiempo_prod`
GROUP BY
    CODIGO_COMPANIA,
    Nivel_de_Riesgo,
    Fecha_Calificacion
ORDER BY
    Fecha_Calificacion DESC,
    CODIGO_COMPANIA,
    Nivel_de_Riesgo;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
Archivo Original: LAFT_laft_comportamiento_score_riesgo
