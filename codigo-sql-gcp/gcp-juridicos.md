# Juridicos

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Varios

## Codigo SQL

```sql
SELECT 
    NUMERO_SECUENCIA_POLIZA,
    TIPO_DOCUMENTO,
    KEY_ID,
    NOMBRE,
    NOMBRE_COMERCIAL,
    SECTOR_ECONOMICO,
    ACTIVIDAD_ECONOMICA,
    CODIGO_CIIU,
    FECHA_NACIMIENTO,
    GRAN_CONTRIBUYENTE,
    NUMERO_EMPLEADOS,
    VALOR_EGRESOS,
    VALOR_INGRESOS,
    VALOR_ACTIVO,
    VALOR_PASIVO,
    VALOR_PATRIMONIO,
    FECHA_ACTUALIZACION,
    AUTORIZA_RECIBIR_INF,
    AUTORIZA_COMPARTIR_INF
FROM 
    sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_juridicos;

Sheet - > https://docs.google.com/spreadsheets/d/1x21ZsyjeoWqu2X4zwHSH3x1GLVaJ0mtR3s5EyiFZ8Jg/edit?gid=1183022185#gid=1183022185

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
