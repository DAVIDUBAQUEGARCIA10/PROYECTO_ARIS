# Biblioteca

## Descripcion
Script de sql para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: Oracle
Categoria: Varios

## Codigo SQL

```sql
SELECT 
    syn.synonym_name       AS alias_publico,
    col.owner              AS propietario_real,
    col.table_name         AS nombre_real_tabla,
    col.column_id,
    col.column_name        AS nombre_columna,
    col.data_type          AS tipo_dato,
    col.data_length        AS longitud,
    col.nullable           AS permite_nulos
FROM 
    all_synonyms syn
JOIN 
    all_tab_columns col 
    ON syn.table_name = col.table_name AND syn.table_owner = col.owner
WHERE 
    syn.owner = 'PUBLIC'
    AND UPPER(col.column_name) LIKE '%REFERENCIA%'
ORDER BY 
    syn.synonym_name, col.column_id;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
