# Nomina Activa

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Varios

## Codigo SQL

```sql
SELECT DISTINCT
    c.TIPO_DOCUMENTO,
    t.NUMERO_DOCUMENTO,
    c.KEY_ID,
    c.FUENTE,
    c.EMPRESA_RELACION,
    c.EMPRESA_CONTRATANTE_NOMBRE,
    c.CLASE_VINCULACION,
    c.DESCRIPCION_TIPO_RELACION,
    c.NOMBRE_DEPARTAMENTO_EMPRESA,
    c.FECHA_CIERRE
FROM `sb-ecosistemaanalitico-lago.people_analytics_prod.v_colaboradores_activos` c

LEFT JOIN `sb-sandbox-usuarios.sandbox_cumplimiento.t_terceros_clientes` t
    ON c.TIPO_DOCUMENTO = t.TIPO_DOCUMENTO
   AND c.KEY_ID = t.KEY_ID

WHERE c.FECHA_CIERRE = (
    SELECT MAX(FECHA_CIERRE)
    FROM `sb-ecosistemaanalitico-lago.people_analytics_prod.v_colaboradores_activos`
)

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
