# Sorteos Capi

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Varios

## Codigo SQL

```sql
SELECT 
    CODIGO_COMPANIA,
    TIPO_DOCUMENTO,
    KEY_ID_TOMADOR,
    NUMERO_TITULO,
    FECHA_TRANSACCION,
    CODIGO_TIPO_MOVIMIENTO,
    NUMERO_TRANSACCION,
    VALOR_TRANSACCION,
    CODIGO_AGENCIA,
    AGRUPAMIENTO_CODIGO,
    SIGNO_MOVIMIENTO,
    DESCRIPCION_TIPO_MOVIMIENTO,
    ABREVIATURA_TIPO_MOVIMIENTO,
    VALOR_CUOTA_ACTUAL,
    VALOR_CUOTA_INICIAL,
    CODIGO_PLAN,
    FECHA_MODIFICACION,
    FECHA_CREACION,
    FECHA_BLOQUEO,
    FECHA_CANCELACION
FROM 
    `sb-ecosistemaanalitico-lago.capitalizadora.t_movimientos_capitalizadora`
WHERE 
    AGRUPAMIENTO_CODIGO = 'SORTEO';

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
