# Canales Score

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Score

## Codigo SQL

```sql

SELECT 
    ID,
    Codigo_Canal_de_vinculacion,
    Canal_de_vinculacion,
    Nivel_de_riesgo,
    ESTADO
FROM 
    `sb-sandbox-usuarios.sandbox_cumplimiento.t_canal_nivel_riesgo`;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
