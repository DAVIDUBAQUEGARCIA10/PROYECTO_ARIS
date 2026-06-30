# Log De Modificaciones

## Descripcion
Script de sql para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: Oracle
Categoria: Varios

## Codigo SQL

```sql
SELECT DISTINCT
n.TIPDOC_CODIGO,
    n.NUMERO_DOCUMENTO,
    h.FECHA_MODIFICACION,
    h.MEDCOM_SECUENCIA,
    h.FECHA_MODIFICACION,
    h.USUARIO_MODIFICACION
FROM historico_medios_comunicacion h

INNER JOIN medios_comunicacion mc
    ON h.MEDCOM_SECUENCIA = mc.SECUENCIA

INNER JOIN naturales n
    ON mc.NAT_SECUENCIA = n.SECUENCIA

WHERE h.FECHA_MODIFICACION >= DATE '2026-02-01'
  AND h.FECHA_MODIFICACION <  DATE '2026-03-01';






select * from naturales
where numero_documento  = 16548724 -- 45700

 select * from medios_comunicacion
 where NAT_SECUENCIA = 45700
 
 
 select *
 from historico_medios_comunicacion
 where  medcom_secuencia = 28525825
 
 --28525825
 ---28467005
 select * 
 from detalle_cuenta_multifondo 

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
