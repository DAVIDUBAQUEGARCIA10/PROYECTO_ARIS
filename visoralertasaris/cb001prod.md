# cb_001_prod

## Descripcion
Se genera alerta cuando un cliente de Capitalizadora Bolívar presenta coincidencia en listas consultables por tipo y número de identificación bajo las restricciones 1, 2, 5, 20, 24, 25, 26, y no existe registro en la base de atención de SIMASOL por tipo y número de identificación.

## Tipo
Archivo: BigQuery SQL
Carpeta: VISOR_ALERTAS_ARIS
Tabla destino: cumplimiento_normativo_prod.cb_001_prod

## Contenido

```
INSERT INTO sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.cb_001_prod

SELECT DISTINCT
    c.KEY_ID_BENEFICIARIO as KEY_ID,          -- ID del cliente
    c.TIPO_DOCUMENTO,                          -- Tipo de documento del cliente
    c.TITULO as NUMERO_TITULO,                 -- Título del cliente en Capitalizadora
    r.CODIGO_RESTRICCION,                      -- Código de restricción (1,2,5,20,24,25,26)
    r.DESCRIPCION as DESCRIPCION_RESTRICCION,  -- Descripción de la restricción

    -- Marca del mes de alerta (formato Año-Mes)
    FORMAT_DATE('%Y-%m', CURRENT_DATE()) AS FECHA_ALERTA_MES,

    -- Fecha en la que se genera la alerta
    CURRENT_DATE() AS FECHA_ALERTA
FROM
    sb-ecosistemaanalitico-lago.capitalizadora.t_clientes_por_titulo_capitalizadora c

-- Cruce con listas restrictivas por tipo y número de identificación
JOIN
    sb-ecosistemaanalitico-lago.seguros_bolivar.t_terceros_restringidos r
    ON c.TIPO_DOCUMENTO = r.TIPO_DOCUMENTO_TERCERO
    AND c.KEY_ID_BENEFICIARIO = r.KEY_ID

-- Filtro por restricciones consultables y activas
WHERE r.CODIGO_RESTRICCION IN (1, 2, 5, 20, 24, 25, 26)
    AND r.FECHA_BAJA IS NULL

    -- No existe registro en la base de atención de SIMASOL (por tipo y número de identificación)
    AND NOT EXISTS (
        SELECT 1
        FROM sb-ecosistemaanalitico-lago.operaciones_log_fac.t_solicitudes_prevencion_lavado s
        WHERE s.TIPO_DOCUMENTO = c.TIPO_DOCUMENTO
          AND s.KEY_ID = c.KEY_ID_BENEFICIARIO
    )

    -- Anti-duplicados por mes
    AND NOT EXISTS (
        SELECT 1
        FROM sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.cb_001_prod t
        WHERE t.KEY_ID = c.KEY_ID_BENEFICIARIO
          AND t.TIPO_DOCUMENTO = c.TIPO_DOCUMENTO
          AND t.NUMERO_TITULO = c.TITULO
          AND t.CODIGO_RESTRICCION = r.CODIGO_RESTRICCION
          AND t.FECHA_ALERTA_MES = FORMAT_DATE('%Y-%m', CURRENT_DATE())
    )
ORDER BY c.KEY_ID_BENEFICIARIO;

```

## Campos del Output

| Campo | Descripción |
|-------|-------------|
| KEY_ID | Número de identificación del cliente |
| TIPO_DOCUMENTO | Tipo de documento (CC, CE, NIT, etc.) |
| NUMERO_TITULO | Título del cliente en Capitalizadora |
| CODIGO_RESTRICCION | Código de restricción (1,2,5,20,24,25,26) |
| DESCRIPCION_RESTRICCION | Descripción de la restricción |
| FECHA_ALERTA_MES | Año-Mes del procesamiento (YYYY-MM) |
| FECHA_ALERTA | Fecha exacta de la alerta |

## Tablas Utilizadas

- sb-ecosistemaanalitico-lago.capitalizadora.t_clientes_por_titulo_capitalizadora
- sb-ecosistemaanalitico-lago.seguros_bolivar.t_terceros_restringidos
- sb-ecosistemaanalitico-lago.operaciones_log_fac.t_solicitudes_prevencion_lavado (base de atención SIMASOL)

## Validaciones

- Cliente de Capitalizadora Bolívar
- Coincidencia por TIPO_DOCUMENTO + KEY_ID (tipo y número de identificación)
- Restricciones consultables: 1, 2, 5, 20, 24, 25, 26
- Solo restricciones activas (FECHA_BAJA IS NULL)
- No existe registro en la base de atención de SIMASOL (t_solicitudes_prevencion_lavado) por tipo y número de identificación
- Anti-duplicados por mes (NOT EXISTS)

---

Fecha: 2026-06-30
Proyecto: ARIS - Seguros Bolivar
Archivo Original: cb_001_prod