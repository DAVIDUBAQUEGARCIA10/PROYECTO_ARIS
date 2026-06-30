# Base De Terceros Restringidos

## Descripcion
Script de sql para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: Oracle
Categoria: Terceros

## Codigo SQL

```sql
SELECT * 
FROM clientes_restringidos cr
JOIN fuentes_restriccion fr
ON cr.FUERES_CODIGO = fr.CODIGO;


SELECT COUNT(*) 
FROM clientes_restringidos cr
JOIN fuentes_restriccion fr
ON cr.FUERES_CODIGO = fr.CODIGO
where fr.marca_activo = 'A'


SELECT NUMERO_DOCUMENTO, TIPDOC_CODIGO, NOMBRES, CODIGO, DESCRIPCION -- aca la llave es el numero de documento y tipo de documento
FROM clientes_restringidos cr
JOIN fuentes_restriccion fr
ON cr.FUERES_CODIGO = fr.CODIGO
where FECHA_BAJA is null

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
