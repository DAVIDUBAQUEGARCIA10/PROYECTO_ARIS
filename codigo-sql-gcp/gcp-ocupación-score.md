# Ocupación Score

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Score

## Codigo SQL

```sql

SELECT 
    ID,
    OCUPACION,
    NIVEL_DE_RIESGO,
    ESTADO
FROM 
    `sb-sandbox-usuarios.sandbox_cumplimiento.t_ocupacion_nivel_riesgo`;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
