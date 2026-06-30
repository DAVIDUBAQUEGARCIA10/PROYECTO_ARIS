# Nacionalidad Score

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Score

## Codigo SQL

```sql
SELECT 
    ID,
    NOMBRE,
    Riesgo_LAFT_Basilea,
    Nivel_de_riesgo_asignado
FROM 
    `sb-sandbox-usuarios.sandbox_cumplimiento.t_nacionalidad_nivel_riesgo`;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
