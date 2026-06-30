# Juridicos Terceros

## Descripcion
Script de sql para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: Oracle
Categoria: Terceros

## Codigo SQL

```sql
SELECT *
FROM juridicos
LEFT JOIN acteco_tercero ON juridicos.SECUENCIA = acteco_tercero.jur_secuencia
LEFT JOIN actividades_economicas  ON acteco_tercero.acteco_secuencia = actividades_economicas.secuencia 
where (juridicos.fecha_creacion >= '31/07/24' or juridicos.fecha_modificacion >= '30/09/24')

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
