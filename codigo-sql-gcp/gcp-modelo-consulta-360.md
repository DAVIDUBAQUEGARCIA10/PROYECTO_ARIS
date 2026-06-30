# Modelo Consulta 360

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Varios

## Codigo SQL

```sql
select * 
from sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.consulta_contrapartes_360
where KEY_ID_TOMADOR =  "8110123048"

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
