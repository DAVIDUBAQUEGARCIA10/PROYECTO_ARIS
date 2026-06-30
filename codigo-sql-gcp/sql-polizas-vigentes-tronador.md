# Polizas Vigentes Tronador

## Descripcion
Script de sql para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: Oracle
Categoria: Polizas

## Codigo SQL

```sql
e SELECT * FROM (
    SELECT DISTINCT COD_CIA,tdoc_tercero  ,nro_documto,NUM_POL1, cod_ramo,fecha_Emi, fecha_emi_end, FECHA_VENC_POL,NUM_SECU_POL,COD_PROD, MCA_ANU_POL,ROWNUM AS rn
    FROM A2000030 
    WHERE  FECHA_VENC_POL >= '20/09/24'
) WHERE rn > 1 AND rn <1500000



    SELECT DISTINCT COD_CIA,tdoc_tercero  ,nro_documto,NUM_POL1, COD_SECC,cod_ramo,fecha_Emi, fecha_emi_end, FECHA_VENC_POL,NUM_SECU_POL,COD_PROD, MCA_ANU_POL,ROWNUM AS rn
    FROM A2000030 
    WHERE  FECHA_VENC_POL >= '16/03/25' and COD_SECC = 50

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
