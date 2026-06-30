# Consulta De Tomador Asegurado Beneficiario

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Beneficiarios

## Codigo SQL

```sql
SELECT *,
  CASE 
    WHEN KEY_ID_TOMADOR IN ("901005021","901108792") THEN 'TOMADOR'
    WHEN KEY_ID_ASEGURADO IN ("901005021","901108792") THEN 'ASEGURADO'
    WHEN KEY_ID_BENEFICIARIO IN ("901005021","901108792") THEN 'BENEFICIARIO'
  END AS ROL_COINCIDENCIA
FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_tom_ase_ben_vigentes`
WHERE 
  KEY_ID_BENEFICIARIO IN ("901005021","901108792")
  OR KEY_ID_ASEGURADO IN ("901005021","901108792")
  OR KEY_ID_TOMADOR IN ("901005021","901108792");

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
