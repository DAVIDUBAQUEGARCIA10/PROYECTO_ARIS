# cb_001_prod

## Descripcion
Script para generar alertas cuando un cliente de Capitalizadora Bolívar presenta coincidencia en listas consultables por tipo y número de identificación bajo las restricciones 1, 2, 5, 20, 24, 25, 26.

## Tipo
Archivo: BigQuery SQL
Carpeta: VISOR_ALERTAS_ARIS
Tabla destino: cumplimiento_normativo_prod.cb_001_prod

## Logica de Negocio

**Objetivo**: Detectar clientes de Capitalizadora Bolívar que están en listas restrictivas por tipo y número de identificación.

**Restricciones monitoreadas**: 1, 2, 5, 20, 24, 25, 26

**Validaciones**:
1. Cliente es titular en Capitalizadora Bolívar
2. Coincidencia en listas restrictivas por TIPO_DOCUMENTO + KEY_ID
3. Solo restricciones activas (FECHA_BAJA IS NULL)

## Contenido

```sql
-- ============================================================================
-- INSERT a CB_001_PROD
-- Regla: Cliente Capitalizadora Bolivar en listas restrictivas
--        (codigos 1,2,5,20,24,25,26) por tipo y numero de identificacion
-- Validaciones: TIPO_DOCUMENTO + KEY_ID
-- Anti-duplicados: NOT EXISTS por (KEY_ID, TIPO_DOCUMENTO, TITULO,
--                                  CODIGO_RESTRICCION, FECHA_ALERTA_MES)
-- ============================================================================

-- PASO 1: CREATE TABLE si no existe
CREATE TABLE IF NOT EXISTS `sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.cb_001_prod` (
  CODIGO_COMPANIA                INT64,
  KEY_ID                         STRING,
  TIPO_DOCUMENTO                 STRING,
  NUMERO_TITULO                  STRING,
  CODIGO_RESTRICCION             INT64,
  DESCRIPCION_RESTRICCION        STRING,
  MOTIVO_ALERTA                  STRING,
  FECHA_ALERTA_MES               STRING,
  FECHA_ALERTA                   DATE,
  FECHA_MODIFICACION             TIMESTAMP
);

-- PASO 2: INSERT con logica de validacion
INSERT INTO `sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.cb_001_prod` (
  CODIGO_COMPANIA,
  KEY_ID,
  TIPO_DOCUMENTO,
  NUMERO_TITULO,
  CODIGO_RESTRICCION,
  DESCRIPCION_RESTRICCION,
  MOTIVO_ALERTA,
  FECHA_ALERTA_MES,
  FECHA_ALERTA,
  FECHA_MODIFICACION
)

WITH clientes_cap AS (
  /* ===================== 1️⃣ Obtener clientes de Capitalizadora Bolivar ===================== */
  SELECT DISTINCT
    1                          AS CODIGO_COMPANIA,
    c.KEY_ID_BENEFICIARIO      AS KEY_ID,
    c.TIPO_DOCUMENTO,
    c.TITULO                   AS NUMERO_TITULO
  FROM `sb-ecosistemaanalitico-lago.capitalizadora.t_clientes_por_titulo_capitalizadora` c
),

restricciones AS (
  /* ===================== 2️⃣ Obtener terceros en listas restrictivas ===================== */
  SELECT DISTINCT
    TIPO_DOCUMENTO_TERCERO,
    KEY_ID,
    CODIGO_RESTRICCION,
    DESCRIPCION
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_terceros_restringidos`
  WHERE CODIGO_RESTRICCION IN (1, 2, 5, 20, 24, 25, 26)
    AND FECHA_BAJA IS NULL
),

con_alerta AS (
  /* ===================== 3️⃣ Cruce cliente Capitalizadora vs listas restrictivas ===================== */
  SELECT
    s.CODIGO_COMPANIA,
    s.KEY_ID,
    s.TIPO_DOCUMENTO,
    s.NUMERO_TITULO,
    r.CODIGO_RESTRICCION,
    r.DESCRIPCION                        AS DESCRIPCION_RESTRICCION,
    CONCAT(
      'CLIENTE CAPITALIZADORA BOLIVAR EN LISTA RESTRICTIVA (',
      CAST(r.CODIGO_RESTRICCION AS STRING),
      '): ',
      r.DESCRIPCION
    ) AS MOTIVO_ALERTA,
    FORMAT_DATE('%Y-%m', CURRENT_DATE()) AS FECHA_ALERTA_MES,
    CURRENT_DATE()                       AS FECHA_ALERTA,
    CURRENT_TIMESTAMP()                  AS FECHA_MODIFICACION
  FROM clientes_cap s
  JOIN restricciones r
    ON s.TIPO_DOCUMENTO = r.TIPO_DOCUMENTO_TERCERO
   AND s.KEY_ID         = r.KEY_ID
),

final AS (
  /* ===================== 4️⃣ Deduplicacion por mes ===================== */
  SELECT
    *,
    ROW_NUMBER() OVER (
      PARTITION BY KEY_ID, TIPO_DOCUMENTO, NUMERO_TITULO, CODIGO_RESTRICCION, FECHA_ALERTA_MES
      ORDER BY FECHA_MODIFICACION DESC
    ) AS rn
  FROM con_alerta
)

/* ===================== 5️⃣ Inserta evitando duplicados ===================== */
SELECT
  CODIGO_COMPANIA,
  KEY_ID,
  TIPO_DOCUMENTO,
  NUMERO_TITULO,
  CODIGO_RESTRICCION,
  DESCRIPCION_RESTRICCION,
  MOTIVO_ALERTA,
  FECHA_ALERTA_MES,
  FECHA_ALERTA,
  FECHA_MODIFICACION
FROM final
WHERE rn = 1
  AND NOT EXISTS (
    SELECT 1
    FROM `sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.cb_001_prod` t
    WHERE t.KEY_ID             = final.KEY_ID
      AND t.TIPO_DOCUMENTO     = final.TIPO_DOCUMENTO
      AND t.NUMERO_TITULO      = final.NUMERO_TITULO
      AND t.CODIGO_RESTRICCION = final.CODIGO_RESTRICCION
      AND t.FECHA_ALERTA_MES   = final.FECHA_ALERTA_MES
  );
```

## Explicacion de Secciones

### 1️⃣ clientes_cap
- Obtiene clientes de Capitalizadora Bolívar
- Extrae TIPO_DOCUMENTO, KEY_ID y NUMERO_TITULO
- Fuente: t_clientes_por_titulo_capitalizadora

### 2️⃣ restricciones
- Consulta terceros en listas restrictivas
- Filtra por códigos: 1, 2, 5, 20, 24, 25, 26
- Solo restricciones activas (FECHA_BAJA IS NULL)

### 3️⃣ con_alerta
- Cruza clientes Capitalizadora con listas restrictivas
- JOIN por TIPO_DOCUMENTO + KEY_ID
- Genera motivo de alerta descriptivo

### 4️⃣ final
- Deduplicación por (KEY_ID, TIPO_DOCUMENTO, TITULO, RESTRICCION, MES)
- Mantiene un registro por cliente/título/restricción/mes

### 5️⃣ SELECT final
- Inserta evitando duplicados
- NOT EXISTS por (KEY_ID, TIPO_DOCUMENTO, NUMERO_TITULO, CODIGO_RESTRICCION, FECHA_ALERTA_MES)

## Campos del Output

| Campo | Tipo | Descripción |
|-------|------|-------------|
| CODIGO_COMPANIA | INT64 | 1 = Capitalizadora Bolívar |
| KEY_ID | STRING | Número de identificación |
| TIPO_DOCUMENTO | STRING | CC, CE, NIT, etc. |
| NUMERO_TITULO | STRING | Identificador del título |
| CODIGO_RESTRICCION | INT64 | Código de restricción (1,2,5,20,24,25,26) |
| DESCRIPCION_RESTRICCION | STRING | Descripción de la restricción |
| MOTIVO_ALERTA | STRING | Motivo detallado de la alerta |
| FECHA_ALERTA_MES | STRING | Año-Mes del procesamiento (YYYY-MM) |
| FECHA_ALERTA | DATE | Fecha exacta de la alerta |
| FECHA_MODIFICACION | TIMESTAMP | Timestamp del proceso |

## Tablas Utilizadas

- `sb-ecosistemaanalitico-lago.capitalizadora.t_clientes_por_titulo_capitalizadora`
- `sb-ecosistemaanalitico-lago.seguros_bolivar.t_terceros_restringidos`

## Validaciones Criticas

- Cliente de Capitalizadora Bolívar
- Restricción en códigos: 1, 2, 5, 20, 24, 25, 26
- Solo restricciones activas (FECHA_BAJA IS NULL)
- Sin duplicados por (KEY_ID, TIPO_DOCUMENTO, TITULO, RESTRICCION, MES)

---

Fecha: 2026-06-30
Proyecto: ARIS - Seguros Bolivar
Archivo Original: cb_001_prod