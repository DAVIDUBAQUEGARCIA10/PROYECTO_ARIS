# Explorar Variables

## Descripcion
Script de sql para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: Oracle
Categoria: Varios

## Codigo SQL

```sql
SELECT owner, table_name, column_name
FROM all_tab_columns
WHERE UPPER(column_name) LIKE '%id%';

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
