# Traza Consulta Restringidos Intranet

## Descripcion
Script de sql para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: Oracle
Categoria: Varios

## Codigo SQL

```sql
select * from
clientes_restringidos_traza
where cod_secc is not null and fecha >= TO_DATE('01-01-2024', 'DD-MM-YYYY')
order by fecha desc

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
