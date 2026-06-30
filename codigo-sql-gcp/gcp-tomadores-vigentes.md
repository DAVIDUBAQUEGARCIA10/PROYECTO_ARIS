# Tomadores Vigentes

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Clientes

## Codigo SQL

```sql
SELECT COUNT(DISTINCT CONCAT(KEY_ID_TOMADOR)) AS unique_count
from sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_tom_ase_vigentes
where  FECHA_CORTE >= '2024-09-01'

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
