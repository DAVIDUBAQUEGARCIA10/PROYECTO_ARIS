# Actividad Economica Score

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Score

## Codigo SQL

```sql
SELECT 
    ID,
    Codigo_compania,
    Codigo_del_Ramo,
    Nombre_de_ramo,
    Codigo_de_Producto,
    Codigo_Monitor,
    Nombre_del_Producto,
    NIVEL_DE_RIESGO,
    ESTADO
FROM 
    `sb-sandbox-usuarios.sandbox_cumplimiento.t_productos_nivel_riesgo`;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
