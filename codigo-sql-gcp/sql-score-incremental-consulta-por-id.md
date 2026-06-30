# Score Incremental Consulta Por Id

## Descripcion
Script de sql para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: Oracle
Categoria: Score

## Codigo SQL

```sql
select *
from(
SELECT DISTINCT 
  tipdoc_codigo,
  numero_documento, 
  fecha_modificacion,
  REGEXP_REPLACE(
    REPLACE(REPLACE(REPLACE(REPLACE(Razon_social, '.', ''), ',', ''), ';', ''), '/', ''),
    '\s+', ' '
  ) AS Nombre
FROM juridicos 
UNION ALL

SELECT 
  tipdoc_codigo,
  numero_documento, 
  fecha_modificacion,
  REGEXP_REPLACE(
    REPLACE(REPLACE(REPLACE(REPLACE(
      TRIM(
        NVL(PRIMER_NOMBRE, '') || ' ' ||
        NVL(SEGUNDO_NOMBRE, '') || ' ' ||
        NVL(PRIMER_APELLIDO, '') || ' ' ||
        NVL(SEGUNDO_APELLIDO, '')
      ), '.', ''), ',', ''), ';', ''), '/', ''
    ),
    '\s+', ' '
  ) AS Nombre
FROM naturales)
where numero_documento  in( 1000690142)

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
