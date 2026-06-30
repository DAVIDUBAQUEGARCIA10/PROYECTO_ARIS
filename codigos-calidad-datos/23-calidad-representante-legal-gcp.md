# Calidad de Datos: Representante Legal - GCP Davivienda

## Descripción
Extrae representante legal de PJ desde Davivienda. Compara representante legal de ARIS vs Davivienda con normalización a 9 dígitos.

---

## Código SQL

```sql
WITH base AS (
    SELECT DISTINCT TIPO_DOCUMENTO, KEY_ID, SUBSTR(CAST(KEY_ID AS STRING), 1, 9) AS KEY_ID_SIN_DV,
      KEY_ID_REPRESENTANTE_LEGAL
    FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_trazabilidad_formatos_servicios_marcas`
    WHERE TIPO_DOCUMENTO IN ('NT', 'NE')
),

representante_legal AS (
    SELECT TIPO_DOCUMENTO_CLIENTE, KEY_ID_CLIENTE, TIPO_DOCUMENTO_RELACIONADO, KEY_ID_RELACIONADO
    FROM (
        SELECT TIPO_DOCUMENTO_CLIENTE,
          SUBSTR(CAST(KEY_ID_CLIENTE AS STRING), 1, 9) AS KEY_ID_CLIENTE,
          TIPO_DOCUMENTO_RELACIONADO, KEY_ID_RELACIONADO,
          ROW_NUMBER() OVER (PARTITION BY SUBSTR(CAST(KEY_ID_CLIENTE AS STRING), 1, 9)
            ORDER BY KEY_ID_RELACIONADO) AS rn
        FROM `sb-ecosistemaanalitico-lago.davivienda.t_individuos_relacionados_pn_pj`
        WHERE ESTADO_DATO = 'VIGENTE' AND DESCRIPCION_RELACION = 'REPRESENTANTE LEGAL'
    )
    WHERE rn = 1
)

SELECT b.TIPO_DOCUMENTO, b.KEY_ID, b.KEY_ID_REPRESENTANTE_LEGAL,
  r.TIPO_DOCUMENTO_RELACIONADO, r.KEY_ID_RELACIONADO
FROM base b
LEFT JOIN representante_legal r ON b.KEY_ID_SIN_DV = r.KEY_ID_CLIENTE;
```

---

**Categoría**: Calidad de Datos - Representante Legal  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago.davivienda`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29