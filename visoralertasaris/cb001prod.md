# cb_001_prod

## Descripcion
Se genera alerta cuando un cliente de Capitalizadora Bolívar presenta coincidencia en listas consultables por tipo y número de identificación bajo las restricciones 1, 2, 5, 20, 24, 25, 26, y no existe registro en la base de atención de SIMASOL por tipo y número de identificación.

## Tipo
Archivo: BigQuery SQL
Carpeta: VISOR_ALERTAS_ARIS
Tabla destino: cumplimiento_normativo_prod.cb_001_prod

## CREATE TABLE

```
CREATE TABLE IF NOT EXISTS `sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.cb_001_prod` (
  FECHA_TRANSACCION            DATE,      -- Fecha del movimiento
  NUMERO_TITULO               STRING,    -- Título del movimiento
  VALOR_TRANSACCION           NUMERIC,   -- Valor de la transacción
  DESCRIPCION_TIPO_MOVIMIENTO STRING,    -- Tipo de movimiento
  CODIGO_AGENCIA              STRING,    -- Agencia que realizó el movimiento
  KEY_ID                      STRING,    -- Número de identificación del cliente
  TIPO_DOCUMENTO              STRING,    -- Tipo de documento del cliente
  CODIGO_RESTRICCION          INT64,     -- Código de restricción (1,2,5,20,24,25,26)
  DESCRIPCION_RESTRICCION     STRING,    -- Descripción de la restricción
  VALIDACION_LISTAS           BOOL,      -- Bandera de validación en listas restrictivas
  DESCRIPCION_VALIDACION      STRING,    -- Texto explicativo de la alerta
  FECHA_ALERTA_MES            STRING,    -- Año-Mes del procesamiento (YYYY-MM)
  FECHA_ALERTA                DATE       -- Fecha exacta de la alerta
);
```

## INSERT

```
INSERT INTO `sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.cb_001_prod`

WITH clientes_capitalizadora AS (
  /* ===================== 1️⃣ Clientes de Capitalizadora Bolívar con movimientos ===================== */
  SELECT DISTINCT
    m.FECHA_TRANSACCION,
    m.NUMERO_TITULO,
    m.VALOR_TRANSACCION,
    m.DESCRIPCION_TIPO_MOVIMIENTO,
    m.CODIGO_AGENCIA,
    c.KEY_ID_BENEFICIARIO AS KEY_ID,
    c.TIPO_DOCUMENTO
  FROM `sb-ecosistemaanalitico-lago.capitalizadora.t_movimientos_capitalizadora` m
  JOIN `sb-ecosistemaanalitico-lago.capitalizadora.t_clientes_por_titulo_capitalizadora` c
    ON m.NUMERO_TITULO = c.TITULO
  WHERE m.FECHA_TRANSACCION >= '2026-03-01'
),

restringidos AS (
  /* ===================== 2️⃣ Terceros en listas restrictivas (1,2,5,20,24,25,26) ===================== */
  SELECT DISTINCT
    TIPO_DOCUMENTO_TERCERO,
    KEY_ID,
    CODIGO_RESTRICCION,
    DESCRIPCION
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_terceros_restringidos`
  WHERE CODIGO_RESTRICCION IN (1, 2, 5, 20, 24, 25, 26)
    AND FECHA_BAJA IS NULL
),

simasol AS (
  /* ===================== 3️⃣ Base de atención SIMASOL ===================== */
  SELECT DISTINCT
    TIPO_DOCUMENTO,
    KEY_ID
  FROM `sb-ecosistemaanalitico-lago.operaciones_log_fac.t_solicitudes_prevencion_lavado`
)

/* ===================== 4️⃣ Cruce final: Capitalizadora en listas restrictivas SIN registro en SIMASOL ===================== */
SELECT DISTINCT
  c.FECHA_TRANSACCION,
  c.NUMERO_TITULO,
  c.VALOR_TRANSACCION,
  c.DESCRIPCION_TIPO_MOVIMIENTO,
  c.CODIGO_AGENCIA,
  c.KEY_ID,
  c.TIPO_DOCUMENTO,

  r.CODIGO_RESTRICCION,
  r.DESCRIPCION AS DESCRIPCION_RESTRICCION,

  TRUE AS VALIDACION_LISTAS,

  CONCAT(
    'Cliente de Capitalizadora Bolívar',
    ' presenta coincidencia vigente en listas/restricciones consultables con código ',
    CAST(r.CODIGO_RESTRICCION AS STRING),
    ' - ',
    COALESCE(r.DESCRIPCION, 'Sin descripción'),
    '. No registra atención previa en SIMASOL.'
  ) AS DESCRIPCION_VALIDACION,

  FORMAT_DATE('%Y-%m', CURRENT_DATE()) AS FECHA_ALERTA_MES,
  CURRENT_DATE() AS FECHA_ALERTA

FROM clientes_capitalizadora c
JOIN restringidos r
  ON c.KEY_ID = r.KEY_ID
 AND c.TIPO_DOCUMENTO = r.TIPO_DOCUMENTO_TERCERO

WHERE NOT EXISTS (
  SELECT 1
  FROM simasol s
  WHERE s.KEY_ID = c.KEY_ID
    AND s.TIPO_DOCUMENTO = c.TIPO_DOCUMENTO
);
```

## Explicacion de Secciones

### 1️⃣ clientes_capitalizadora
- Obtiene movimientos de clientes de Capitalizadora Bolívar
- JOIN entre movimientos y clientes por título
- Filtra transacciones desde 2026-03-01

### 2️⃣ restringidos
- Consulta terceros en listas restrictivas
- Filtra por códigos: 1, 2, 5, 20, 24, 25, 26
- Solo restricciones activas (FECHA_BAJA IS NULL)

### 3️⃣ simasol
- Base de atención de SIMASOL (prevención de lavado)
- Extrae tipo y número de identificación

### 4️⃣ SELECT final
- Cruza clientes Capitalizadora con listas restrictivas (por tipo + número identificación)
- Excluye clientes que YA tienen registro en SIMASOL (NOT EXISTS)
- Genera bandera VALIDACION_LISTAS y descripción explicativa de la alerta
- Agrega marca de mes y fecha de alerta

## Campos del Output

| Campo | Descripción |
|-------|-------------|
| FECHA_TRANSACCION | Fecha del movimiento |
| NUMERO_TITULO | Título del movimiento |
| VALOR_TRANSACCION | Valor de la transacción |
| DESCRIPCION_TIPO_MOVIMIENTO | Tipo de movimiento |
| CODIGO_AGENCIA | Agencia que realizó el movimiento |
| KEY_ID | Número de identificación del cliente |
| TIPO_DOCUMENTO | Tipo de documento del cliente |
| CODIGO_RESTRICCION | Código de restricción (1,2,5,20,24,25,26) |
| DESCRIPCION_RESTRICCION | Descripción de la restricción |
| VALIDACION_LISTAS | Bandera de validación en listas restrictivas |
| DESCRIPCION_VALIDACION | Texto explicativo de la alerta |
| FECHA_ALERTA_MES | Año-Mes del procesamiento (YYYY-MM) |
| FECHA_ALERTA | Fecha exacta de la alerta |

## Tablas Utilizadas

- sb-ecosistemaanalitico-lago.capitalizadora.t_movimientos_capitalizadora
- sb-ecosistemaanalitico-lago.capitalizadora.t_clientes_por_titulo_capitalizadora
- sb-ecosistemaanalitico-lago.seguros_bolivar.t_terceros_restringidos
- sb-ecosistemaanalitico-lago.operaciones_log_fac.t_solicitudes_prevencion_lavado (base de atención SIMASOL)

## Validaciones

- Cliente de Capitalizadora Bolívar con movimientos desde 2026-03-01
- Coincidencia por TIPO_DOCUMENTO + KEY_ID (tipo y número de identificación)
- Restricciones consultables: 1, 2, 5, 20, 24, 25, 26
- Solo restricciones activas (FECHA_BAJA IS NULL)
- No existe registro en la base de atención de SIMASOL (t_solicitudes_prevencion_lavado) por tipo y número de identificación

---

Fecha: 2026-06-30
Proyecto: ARIS - Seguros Bolivar
Archivo Original: cb_001_prod