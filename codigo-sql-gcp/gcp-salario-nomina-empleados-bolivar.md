# Salario Nomina Empleados Bolivar

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Varios

## Codigo SQL

```sql
SELECT * 
FROM `sb-ecosistemaanalitico-lago.talento_humano.t_asignacion_mensual`
WHERE FECHA_CORTE = (
  SELECT MAX(FECHA_CORTE)
  FROM `sb-ecosistemaanalitico-lago.talento_humano.t_asignacion_mensual`
);

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
