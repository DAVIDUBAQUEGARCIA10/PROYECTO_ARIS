# Tabla De Desencripción

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Varios

## Codigo SQL

```sql
CREATE OR REPLACE TABLE sb-sandbox-usuarios.sandbox_cumplimiento.t_terceros_clientes AS

SELECT distinct
    t1.TIPO_DOCUMENTO, 
    t1.NUMERO_DOCUMENTO, 
    t2.KEY_ID,
    t2.SERIAL AS SERIAL_ENCRIPTADO
FROM (select *
      from sb-sandbox-usuarios.sandbox_cumplimiento.t_terceros_sin_encriptar 
      union all
      select *
      from sb-sandbox-usuarios.sandbox_cumplimiento.t_terceros_sin_encriptar_reciclaje ) t1  
JOIN (select * 
      from sb-sandbox-usuarios.sandbox_cumplimiento.t_terceros_encriptados 
      union all
      select * 
      from sb-sandbox-usuarios.sandbox_cumplimiento.t_terceros_encriptados_reciclaje) t2
ON t1.SERIAL = t2.SERIAL;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
