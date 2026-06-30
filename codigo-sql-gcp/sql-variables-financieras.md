# Variables Financieras

## Descripcion
Script de sql para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: Oracle
Categoria: Varios

## Codigo SQL

```sql
select *
from   estados_financieros ef
where  ef.nat_secuencia = (select secuencia
                           from naturales
                           where  numero_documento = 79295481)
  and  ef.secuencia = (select max(ef1.secuencia)
                       from   estados_financieros ef1
                       where  ef1.nat_secuencia = ef.nat_secuencia)
                       

select *
from   estados_financieros ef
where  ef.jur_secuencia = (select secuencia
                           from juridicos
                           where  numero_documento = 860034313)
  and  ef.secuencia = (select max(ef1.secuencia)
                       from   estados_financieros ef1
                       where  ef1.jur_secuencia = ef.jur_secuencia)
                       
                       

                       

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
