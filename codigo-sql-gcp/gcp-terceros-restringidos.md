# Terceros Restringidos

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Terceros

## Codigo SQL

```sql
SELECT
    TIPO_DOCUMENTO_TERCERO,
    KEY_ID,
    CODIGO_RESTRICCION,
    DESCRIPCION,
    FECHA_BAJA,
    FECHA_CREACION,
    FECHA_MODIFICACION,
    VALOR_RIESGO,
    NIVEL_ALERTA,
    MARCA_ACTIVO
FROM
    `sb-ecosistemaanalitico-lago.seguros_bolivar.t_terceros_restringidos`;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
