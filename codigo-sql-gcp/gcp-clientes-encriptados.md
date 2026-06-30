# Clientes Encriptados

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Clientes

## Codigo SQL

```sql
SELECT
    TIPO_DOCUMENTO,
    KEY_ID,
    NUMERO_DOCUMENTO,
    CODIGO_ENTIDAD_COMPANIA,
    FECHA_CALIFICACION,
    NIVEL_DE_RIESGO,
    SERIAL
FROM
    `sb-sandbox-usuarios.sandbox_cumplimiento.t_terceros_clientes`;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
