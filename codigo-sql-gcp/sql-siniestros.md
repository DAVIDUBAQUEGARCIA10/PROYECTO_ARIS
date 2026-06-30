# Siniestros

## Descripcion
Script de sql para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: Oracle
Categoria: Siniestros

## Codigo SQL

```sql
select *
from A3001700  
where cod_benef = 1014282242


select COD_CIA, COD_SECC,NUM_SINI, COD_BENEF, APE_BENEF, NOM_BENEF, FECHA_FACTURA, FECHA_LIQ, FECHA_PAGO, TOTAL_BRUTO_LIQ, OBSERVACION, NUM_ORD_PAGO
from A3001700  
where FECHA_PAGO = '01/01/26'


SELECT DISTINCT 
    COD_CIA,
    tdoc_tercero,
    nro_documto,
    NUM_POL1, --- ACA LA LLAVE ES EL NUMERO DE LA POLIZA PERO SIN LOS DOS ULTIMOS DIGITOS DE IZQUIERDA A DERECHA 
    cod_ramo,
    fecha_Emi,
    fecha_emi_end,
    FECHA_VENC_POL,
    COD_SECC AS RAMO,
    COD_RAMO AS PRODUCTO
FROM A2000030
WHERE FECHA_VENC_POL >= TRUNC(SYSDATE)
and MCA_ANU_POL != 'S';

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
