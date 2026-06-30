# Calidad de Datos: Beneficiario Final - GCP

## Descripción
Análisis de beneficiarios finales desde tabla de beneficiarios con información agregada de composición accionaria. Incluye sumatoria de participación y conteo de accionistas con análisis temporal.

---

## Código SQL

```sql
WITH base AS (

    SELECT DISTINCT
        TIPO_DOCUMENTO,
        KEY_ID

    FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_trazabilidad_formatos_servicios_marcas`

    WHERE TIPO_DOCUMENTO IN ('NT', 'NE')
),

bolivar_detalle AS (

    SELECT
        TIPO_DOCUMENTO,
        KEY_ID,
        CODIGO_TIPO_DOCUMENTO_CONTACTO,
        KEY_ID_CONTACTO,
        PORCENTAJE_PARTICIPACION,
        FECHA_CREACION

    FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_beneficiario_final`

    WHERE PORCENTAJE_PARTICIPACION > 0
      AND MARCA_REGISTRO_ACTIVO = 'A'
),

beneficiarios_bolivar_original AS (

    SELECT
        TIPO_DOCUMENTO,
        KEY_ID,

        SUM(PORCENTAJE_PARTICIPACION)
            AS SUMATORIA_COMPOSICION_ACCIONARIA_BOLIVAR_ORIGINAL,

        COUNT(DISTINCT CONCAT(
            CAST(CODIGO_TIPO_DOCUMENTO_CONTACTO AS STRING),
            '-',
            CAST(KEY_ID_CONTACTO AS STRING)
        )) AS CANTIDAD_ACCIONISTAS_BOLIVAR_ORIGINAL

    FROM bolivar_detalle

    GROUP BY
        TIPO_DOCUMENTO,
        KEY_ID
)

SELECT *
FROM beneficiarios_bolivar_original;
```

---

**Categoría**: Calidad de Datos - Beneficiarios Finales  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29