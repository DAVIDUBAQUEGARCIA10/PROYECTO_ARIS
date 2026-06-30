# Sarlaft Score

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Score

## Codigo SQL

```sql
SELECT 
    COD_COMPA_NIA,
    COMPA_NIA,
    COD_RAMO,
    NOMBRE_RAMO,
    COD_PRODUCTO,
    NOMBRE_DEL_PRODUCTO,
    CANAL_DE_COMERCIALIZACION,
    SEGURO_OBLIGATORIO_VOLUNTARIO,
    POLIZA_INDIVIDUAL_COLECTIVA,
    CLASIFICACION_DE_PRODUCTO_EN_MICROSEGURO_MASIVO,
    DOMINIO,
    SARLAFT_4_0,
    SIMON_VENTAS,
    INTRANET_Y_FILENET,
    NOTAS,
    COMENTARIOS_DIRECCION_LA_FT_Y_T_C
FROM 
    `sb-sandbox-usuarios.sandbox_cumplimiento.t_sarlatf_4_0`;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
