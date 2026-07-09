# cb_001_prod

## Descripcion
Script para generar alertas cuando un cliente de Capitalizadora Bolívar (primer y/o segundo suscriptor) presenta coincidencia en listas consultables por tipo y número de identificación bajo restricciones específicas, validando que no exista registro en SIMASOL ni en matriz ROS.

## Tipo
Archivo: BigQuery SQL
Carpeta: VISOR_ALERTAS_ARIS
Tabla destino: cumplimiento_normativo_prod.cb_001_prod

## Logica de Negocio

**Objetivo**: Detectar clientes de Capitalizadora que están en listas restrictivas pero NO tienen registro en sistemas de atención (SIMASOL) ni en matriz de restricción de operaciones (ROS).

**Restricciones monitoreadas**: 1, 2, 5, 20, 24, 25, 26

**Validaciones**:
1. Cliente es suscriptor (1º o 2º) en Capitalizadora
2. Coincidencia en listas restrictivas por tipo_doc + key_id
3. NO existe en SIMASOL
4. NO existe en matriz ROS
5. Producto vigente

## Contenido

```sql
-- ============================================================================
-- INSERT a CB_001_VISOR_ALERTAS
-- Regla: Cliente Capitalizadora (primer/segundo suscriptor) en listas 
--        restrictivas (1,2,5,20,24,25,26) sin registro en SIMASOL ni ROS
-- Validaciones: tipo_documento + numero_identificacion + nombre_producto
-- Anti-duplicados: NOT EXISTS por (KEY_ID, TITULO, CODIGO_RESTRICCION, 
--                                  FECHA_ALERTA_MES)
-- ============================================================================

-- PASO 1: CREATE TABLE si no existe
CREATE TABLE IF NOT EXISTS `sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.cb_001_prod` (
  CODIGO_COMPANIA                INT64,
  KEY_ID                         STRING,
  TIPO_DOCUMENTO                 STRING,
  NUMERO_TITULO                  STRING,
  NOMBRE_PRODUCTO                STRING,
  ROL_SUSCRIPTOR                 STRING,        -- PRIMER_SUSCRIPTOR o SEGUNDO_SUSCRIPTOR
  FECHA_CREACION_TITULO          DATE,
  CODIGO_RESTRICCION             INT64,
  DESCRIPCION_RESTRICCION        STRING,
  MOTIVO_ALERTA                  STRING,
  EXISTE_EN_SIMASOL              BOOL,
  EXISTE_EN_MATRIZ_ROS           BOOL,
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
  NOMBRE_PRODUCTO,
  ROL_SUSCRIPTOR,
  FECHA_CREACION_TITULO,
  CODIGO_RESTRICCION,
  DESCRIPCION_RESTRICCION,
  MOTIVO_ALERTA,
  EXISTE_EN_SIMASOL,
  EXISTE_EN_MATRIZ_ROS,
  FECHA_ALERTA_MES,
  FECHA_ALERTA,
  FECHA_MODIFICACION
)

WITH suscriptores_cap AS (
  /* ===================== 1️⃣ Obtener suscriptores de Capitalizadora ===================== */
  SELECT DISTINCT
    1                          AS CODIGO_COMPANIA,
    c.KEY_ID_TITULAR           AS KEY_ID,
    c.TIPO_DOCUMENTO,
    m.NUMERO_TITULO,
    COALESCE(m.nombre_producto, 'CAPITALIZADORA BOLIVAR') AS nombre_producto,
    CASE 
      WHEN c.ROW_NUM_TITULAR = 1 THEN 'PRIMER_SUSCRIPTOR'
      WHEN c.ROW_NUM_TITULAR = 2 THEN 'SEGUNDO_SUSCRIPTOR'
    END AS ROL_SUSCRIPTOR,
    DATE(m.FECHA_CREACION)    AS FECHA_CREACION_TITULO,
    ROW_NUMBER() OVER (
      PARTITION BY c.KEY_ID_TITULAR, c.TIPO_DOCUMENTO, m.NUMERO_TITULO
      ORDER BY m.FECHA_CREACION DESC
    ) AS rn
  FROM (
    SELECT
      KEY_ID_TITULAR,
      TIPO_DOCUMENTO,
      TITULO,
      ROW_NUMBER() OVER (
        PARTITION BY KEY_ID_TITULAR, TIPO_DOCUMENTO
        ORDER BY FECHA_CREACION ASC
      ) AS ROW_NUM_TITULAR
    FROM `sb-ecosistemaanalitico-lago.capitalizadora.t_clientes_por_titulo_capitalizadora`
    WHERE FECHA_CANCELACION IS NULL  -- Solo títulos vigentes
  ) c
  JOIN `sb-ecosistemaanalitico-lago.capitalizadora.t_movimientos_capitalizadora` m
    ON c.TITULO = m.NUMERO_TITULO
  WHERE c.ROW_NUM_TITULAR IN (1, 2)  -- Solo primer y segundo suscriptor
),

suscriptores_unicos AS (
  SELECT * FROM suscriptores_cap
  WHERE rn = 1
),

restricciones AS (
  /* ===================== 2️⃣ Obtener terceros en listas restrictivas ===================== */
  SELECT DISTINCT
    TIPO_DOCUMENTO,
    KEY_ID,
    CODIGO_RESTRICCION,
    DESCRIPCION
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_terceros_restringidos`
  WHERE CODIGO_RESTRICCION IN (1, 2, 5, 20, 24, 25, 26)
    AND FECHA_BAJA IS NULL
),

simasol_check AS (
  /* ===================== 3️⃣ Validar si existe en SIMASOL ===================== */
  SELECT DISTINCT
    TIPO_DOCUMENTO,
    KEY_ID,
    TRUE AS existe_simasol
  FROM `sb-ecosistemaanalitico-lago.atenciones_prod.t_simasol`
  WHERE FECHA_BAJA IS NULL
),

ros_check AS (
  /* ===================== 4️⃣ Validar si existe en matriz ROS ===================== */
  SELECT DISTINCT
    TIPO_DOCUMENTO,
    KEY_ID,
    TRUE AS existe_ros
  FROM `sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.matriz_restriccion_operaciones`
  WHERE estado_activo = TRUE
),

con_validaciones AS (
  /* ===================== 5️⃣ Unión con validaciones ===================== */
  SELECT
    s.CODIGO_COMPANIA,
    s.KEY_ID,
    s.TIPO_DOCUMENTO,
    s.NUMERO_TITULO,
    s.NOMBRE_PRODUCTO,
    s.ROL_SUSCRIPTOR,
    s.FECHA_CREACION_TITULO,
    r.CODIGO_RESTRICCION,
    r.DESCRIPCION                AS DESCRIPCION_RESTRICCION,
    CONCAT(
      'CLIENTE CAPITALIZADORA EN LISTA RESTRICTIVA: ',
      r.DESCRIPCION,
      ' | SIN REGISTRO EN SIMASOL: ',
      CASE WHEN COALESCE(sim.existe_simasol, FALSE) THEN 'NO' ELSE 'SI' END,
      ' | SIN REGISTRO EN ROS: ',
      CASE WHEN COALESCE(ros.existe_ros, FALSE) THEN 'NO' ELSE 'SI' END
    ) AS MOTIVO_ALERTA,
    COALESCE(sim.existe_simasol, FALSE)  AS EXISTE_EN_SIMASOL,
    COALESCE(ros.existe_ros, FALSE)      AS EXISTE_EN_MATRIZ_ROS,
    FORMAT_DATE('%Y-%m', CURRENT_DATE()) AS FECHA_ALERTA_MES,
    CURRENT_DATE()                       AS FECHA_ALERTA,
    CURRENT_TIMESTAMP()                  AS FECHA_MODIFICACION
  FROM suscriptores_unicos s
  JOIN restricciones r
    ON s.TIPO_DOCUMENTO = r.TIPO_DOCUMENTO
   AND s.KEY_ID         = r.KEY_ID
  LEFT JOIN simasol_check sim
    ON s.TIPO_DOCUMENTO = sim.TIPO_DOCUMENTO
   AND s.KEY_ID         = sim.KEY_ID
  LEFT JOIN ros_check ros
    ON s.TIPO_DOCUMENTO = ros.TIPO_DOCUMENTO
   AND s.KEY_ID         = ros.KEY_ID
  WHERE COALESCE(sim.existe_simasol, FALSE) = FALSE
    AND COALESCE(ros.existe_ros, FALSE) = FALSE
),

final AS (
  /* ===================== 6️⃣ Deduplicación por mes ===================== */
  SELECT
    *,
    ROW_NUMBER() OVER (
      PARTITION BY KEY_ID, NUMERO_TITULO, CODIGO_RESTRICCION, FECHA_ALERTA_MES
      ORDER BY FECHA_MODIFICACION DESC
    ) AS rn
  FROM con_validaciones
)

/* ===================== 7️⃣ Inserta evitando duplicados ===================== */
SELECT
  CODIGO_COMPANIA,
  KEY_ID,
  TIPO_DOCUMENTO,
  NUMERO_TITULO,
  NOMBRE_PRODUCTO,
  ROL_SUSCRIPTOR,
  FECHA_CREACION_TITULO,
  CODIGO_RESTRICCION,
  DESCRIPCION_RESTRICCION,
  MOTIVO_ALERTA,
  EXISTE_EN_SIMASOL,
  EXISTE_EN_MATRIZ_ROS,
  FECHA_ALERTA_MES,
  FECHA_ALERTA,
  FECHA_MODIFICACION
FROM final
WHERE rn = 1
  AND FECHA_ALERTA >= DATE '2026-03-01'
  AND NOT EXISTS (
    SELECT 1
    FROM `sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.cb_001_prod` t
    WHERE t.KEY_ID                = final.KEY_ID
      AND t.NUMERO_TITULO         = final.NUMERO_TITULO
      AND t.CODIGO_RESTRICCION    = final.CODIGO_RESTRICCION
      AND t.FECHA_ALERTA_MES      = final.FECHA_ALERTA_MES
  );
```

## Explicacion de Secciones

### 1️⃣ **suscriptores_cap**
- Obtiene clientes de Capitalizadora que son primer o segundo suscriptor
- Filtra solo títulos vigentes (FECHA_CANCELACION IS NULL)
- Deduplica por última fecha de creación

### 2️⃣ **restricciones**
- Consulta terceros en listas restrictivas
- Filtra por códigos específicos: 1, 2, 5, 20, 24, 25, 26
- Solo registros activos (FECHA_BAJA IS NULL)

### 3️⃣ **simasol_check**
- Valida si el cliente existe en sistema SIMASOL
- Retorna TRUE si existe en atenciones

### 4️⃣ **ros_check**
- Valida si el cliente existe en matriz de restricción de operaciones
- Retorna TRUE si está registrado en ROS

### 5️⃣ **con_validaciones**
- Unión de todas las validaciones
- Genera motivo de alerta descriptivo
- Establece banderas EXISTE_EN_SIMASOL y EXISTE_EN_MATRIZ_ROS

### 6️⃣ **final**
- Deduplicación por (KEY_ID, TITULO, RESTRICCION, MES)
- Mantiene un registro por cliente/titulo/restriccion/mes

### 7️⃣ **SELECT final**
- Inserta evitando duplicados
- Valida fecha >= 2026-03-01
- NOT EXISTS por (KEY_ID, NUMERO_TITULO, CODIGO_RESTRICCION, FECHA_ALERTA_MES)

## Campos del Output

| Campo | Tipo | Descripción |
|-------|------|-------------|
| CODIGO_COMPANIA | INT64 | 1 = Capitalizadora Bolívar |
| KEY_ID | STRING | Número de identificación |
| TIPO_DOCUMENTO | STRING | CC, CE, NIT, etc. |
| NUMERO_TITULO | STRING | Identificador del producto/título |
| NOMBRE_PRODUCTO | STRING | Nombre del producto de Capitalizadora |
| ROL_SUSCRIPTOR | STRING | PRIMER_SUSCRIPTOR o SEGUNDO_SUSCRIPTOR |
| FECHA_CREACION_TITULO | DATE | Fecha de creación del título |
| CODIGO_RESTRICCION | INT64 | Código de restricción (1,2,5,20,24,25,26) |
| DESCRIPCION_RESTRICCION | STRING | Descripción de la restricción |
| MOTIVO_ALERTA | STRING | Motivo detallado de la alerta |
| EXISTE_EN_SIMASOL | BOOL | Flag si existe en SIMASOL |
| EXISTE_EN_MATRIZ_ROS | BOOL | Flag si existe en matriz ROS |
| FECHA_ALERTA_MES | STRING | Año-Mes del procesamiento (YYYY-MM) |
| FECHA_ALERTA | DATE | Fecha exacta de la alerta |
| FECHA_MODIFICACION | TIMESTAMP | Timestamp del proceso |

## Validaciones Criticas

✅ **Suscriptor válido**: Primer o segundo suscriptor solamente
✅ **Título vigente**: FECHA_CANCELACION IS NULL
✅ **Restricción válida**: Códigos 1, 2, 5, 20, 24, 25, 26
✅ **Sin SIMASOL**: NOT IN SIMASOL
✅ **Sin ROS**: NOT IN matriz_restriccion_operaciones
✅ **Sin duplicados**: Por (KEY_ID, TITULO, RESTRICCION, MES)

## Frecuencia de Ejecución
Recomendado: Diario (preferiblemente post-carga de nuevas restricciones)

## Información de Referencias
- Dataset: `sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod`
- Tablas fuente: Capitalizadora, Terceros restringidos, SIMASOL, ROS
- Período analizado: A partir de 2026-03-01

---

Fecha: 2026-06-30
Proyecto: ARIS - Seguros Bolivar
Archivo Original: cb_001_prod