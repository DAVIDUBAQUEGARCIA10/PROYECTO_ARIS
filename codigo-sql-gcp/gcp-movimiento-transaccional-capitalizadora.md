# Movimiento Transaccional Capitalizadora

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Transaccionalidad

## Codigo SQL

```sql
SELECT
  FECHA_TRANSACCION,
  TIPO_DOCUMENTO,
  KEY_ID,
  NUMERO_TITULO,
  FORMA_PAGO,
  VALOR_TRANSACCION,
  PLAN_CODIGO,
  NOMBRE_DESCRIPCION_TITULO,
  OFICINA
FROM
  `sb-ecosistemaanalitico-lago.capitalizadora.t_transacciones_enriquecida`;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
