# Calidad de Datos: Correo - Personas Naturales

## Descripción
Identifica personas naturales con **correo faltante**. Intenta usar correo de representante legal si el cliente no tiene. Enmascaramiento de datos sensibles.

---

## Código SQL

```sql
WITH base AS (

    SELECT DISTINCT
        TIPO_DOCUMENTO,
        KEY_ID,

        CORREO_ENMASCARADO,
        CORREO_REPRESENTANTE_LEGAL_ENMASCARADO,

        CASE
            WHEN CORREO_ENMASCARADO IS NULL
              OR TRIM(CORREO_ENMASCARADO) = ''
                THEN CORREO_REPRESENTANTE_LEGAL_ENMASCARADO

            ELSE CORREO_ENMASCARADO
        END AS CORREO_FINAL_ENMASCARADO,

        CASE
            WHEN CORREO_ENMASCARADO IS NULL
              OR TRIM(CORREO_ENMASCARADO) = ''
                THEN 'CORREO_TOMADO_REPRESENTANTE_LEGAL'

            ELSE 'CORREO_CLIENTE'
        END AS ORIGEN_CORREO

    FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_trazabilidad_formatos_servicios_marcas`

    WHERE TIPO_DOCUMENTO NOT IN ('NT', 'NE')
)

SELECT
    *

FROM base
WHERE CORREO_ENMASCARADO IS NULL;
```

---

**Categoría**: Calidad de Datos - Contacto  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29