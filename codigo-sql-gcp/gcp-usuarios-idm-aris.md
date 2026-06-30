# Usuarios Idm Aris

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Varios

## Codigo SQL

```sql
select * 
from sb-ecosistemaanalitico-lago.seguros_bolivar.t_idm_accesos
where ROLES_HEREDADOS like'%ARIS%'

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
