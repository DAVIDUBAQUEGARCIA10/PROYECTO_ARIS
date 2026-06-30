# Cliente Con Formato Fatca

## Descripcion
Script de sql para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: Oracle
Categoria: Clientes

## Codigo SQL

```sql
select *
from  fatca_natural

select *
from  fatca_juridica



select n.numero_documento, n.tipdoc_codigo,f.fecha_diligenciamiento
from  fatca_natural  f
join naturales  n
on f.nat_secuencia = n.secuencia
union
select  n.numero_documento, n.tipdoc_codigo,f.fecha_diligenciamiento
from  fatca_juridica  f
join juridicos  n
on f.jur_secuencia = n.secuencia

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
