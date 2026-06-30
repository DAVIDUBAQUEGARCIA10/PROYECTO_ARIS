# Prima Anual

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Varios

## Codigo SQL

```sql
-- ============================================================
-- Suma de PRIMA_ANUAL por NUMERO_POLIZA en el último periodo
-- (según FECHA_PROCESO) con pólizas y endosos vigentes a hoy.
-- Incluye metadatos representativos vía ANY_VALUE().
-- ============================================================

DECLARE periodo_max DATE;

-- 1) Último periodo disponible por FECHA_PROCESO
SET periodo_max = (
  SELECT MAX(DATE(FECHA_PROCESO))
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos`
);

-- 2) Agregación por póliza en el periodo más reciente
SELECT
  -- Llave de agregación
  NUMERO_POLIZA,

  -- Métrica principal
  SUM(PRIMA_ANUAL) AS PRIMA_ANUAL_TOTAL,

  -- Metadatos (mantenidos con ANY_VALUE como pediste)
  ANY_VALUE(PERIODO_FACTURACION) AS PERIODO_FACTURACION_REF,
  ANY_VALUE(CODIGO_RAMO_EMISION) AS CODIGO_RAMO_EMISION_REF,
  ANY_VALUE(NOMBRE_RAMO_EMISION) AS NOMBRE_RAMO_EMISION_REF,
  ANY_VALUE(CODIGO_PRODUCTO)     AS CODIGO_PRODUCTO_REF,
  ANY_VALUE(NOMBRE_PRODUCTO)     AS NOMBRE_PRODUCTO_REF,

  -- 👇 Variables adicionales solicitadas (como referencia)
  ANY_VALUE(CLAVE_AGENTE)     AS CLAVE_AGENTE_REF,
  ANY_VALUE(NOMBRE_AGENTE)    AS NOMBRE_AGENTE_REF,
  ANY_VALUE(MARCA_ANULACION)  AS MARCA_ANULACION_REF,
  ANY_VALUE(CODIGO_LOCALIDAD) AS CODIGO_LOCALIDAD_REF,
  ANY_VALUE(NOMBRE_LOCALIDAD) AS NOMBRE_LOCALIDAD_REF,

  -- Periodo consultado
  periodo_max AS PERIODO_CONSULTADO

FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos`
WHERE
  DATE(FECHA_PROCESO) = periodo_max
  AND DATE(FECHA_FIN_ENDOSO) >= CURRENT_DATE()
  AND DATE(FECHA_FIN_POLIZA)  >= CURRENT_DATE()
  AND KEY_ID_TOMADOR = 'vetBuut3LllceTeWAUNWC6y00J5Iwq3y1jDqetNp1LlfYHB6WmWpewrPfLvLRngvOFEhqsrXnusRvlk+HUL+5Q=='
GROUP BY
  NUMERO_POLIZA, periodo_max
ORDER BY
  PRIMA_ANUAL_TOTAL DESC;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
