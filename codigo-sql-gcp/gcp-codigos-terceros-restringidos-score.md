# Codigos Terceros Restringidos Score

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Terceros

## Codigo SQL

```sql
SELECT 
    ID,
    CODIGO,
    DESCRIPCION,
    NIVEL_DE_RIESGO,
    ESTADO
FROM 
    `sb-sandbox-usuarios.sandbox_cumplimiento.t_categoria_restringido_nivel_riesgo`;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
