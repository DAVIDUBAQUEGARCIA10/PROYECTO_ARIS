# Score_de_terceros_Tabla_D

## Descripcion
Script o consulta del proyecto ARIS.

## Tipo
Archivo: 
Carpeta: Score de Terceros

## Contenido

```
INSERT INTO sb-sandbox-usuarios.sandbox_cumplimiento.Score_de_terceros_Tabla_D
SELECT src.*
FROM sb-sandbox-usuarios.sandbox_cumplimiento.Score_de_terceros_Tabla_C src
LEFT JOIN sb-sandbox-usuarios.sandbox_cumplimiento.Score_de_terceros_Tabla_D tgt
  ON src.TIPO_DOCUMENTO = tgt.TIPO_DOCUMENTO
 AND src.KEY_ID = tgt.KEY_ID
 AND (src.CODIGO_COMPANIA = tgt.CODIGO_COMPANIA OR (src.CODIGO_COMPANIA IS NULL AND tgt.CODIGO_COMPANIA IS NULL))
 AND (src.FECHA_EJECUCION = tgt.FECHA_EJECUCION OR (src.FECHA_EJECUCION IS NULL AND tgt.FECHA_EJECUCION IS NULL))
WHERE tgt.TIPO_DOCUMENTO IS NULL;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
Archivo Original: Score_de_terceros_Tabla_D
