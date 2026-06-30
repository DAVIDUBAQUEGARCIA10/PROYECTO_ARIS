# Jurisdicciones Score

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Score

## Codigo SQL

```sql
SELECT 
    ID,
    CODIGO_DANE_DEL_DEPARTAMENTO,
    DEPARTAMENTO,
    CODIGO_DANE_DEL_MUNICIPIO,
    MUNICIPIO,
    NIVEL_DE_RIESGO_ASIGNADO,
    ESTADO
FROM 
    `sb-sandbox-usuarios.sandbox_cumplimiento.t_jurisdicciones_nivel_riesgo`;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
