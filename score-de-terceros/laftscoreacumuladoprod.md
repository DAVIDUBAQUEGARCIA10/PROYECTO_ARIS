# LAFT_score_acumulado_prod

## Descripcion
Script o consulta del proyecto ARIS.

## Tipo
Archivo: 
Carpeta: Score de Terceros

## Contenido

```
INSERT INTO sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.score_acumulado_en_el_tiempo_prod
SELECT distinct src.*
FROM sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.score_riesgo_prod src
LEFT JOIN sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.score_acumulado_en_el_tiempo_prod tgt
  ON src.TIPO_DOCUMENTO = tgt.TIPO_DOCUMENTO
 AND src.KEY_ID = tgt.KEY_ID
 AND (src.CODIGO_COMPANIA = tgt.CODIGO_COMPANIA OR (src.CODIGO_COMPANIA IS NULL AND tgt.CODIGO_COMPANIA IS NULL))
 AND (src.Fecha_Calificacion = tgt.Fecha_Calificacion OR (src.Fecha_Calificacion IS NULL AND tgt.Fecha_Calificacion IS NULL))
WHERE tgt.TIPO_DOCUMENTO IS NULL;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
Archivo Original: LAFT_score_acumulado_prod
