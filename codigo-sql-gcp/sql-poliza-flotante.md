# Poliza Flotante

## Descripcion
Script de sql para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: Oracle
Categoria: Polizas

## Codigo SQL

```sql
SELECT num_pol1, num_pol_flot
FROM A2000030
WHERE NUM_POL1 IS NOT NULL and    FECHA_VENC_POL >= '05/11/24' and cod_secc = 21;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
