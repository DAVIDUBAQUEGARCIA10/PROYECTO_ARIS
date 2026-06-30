# Empleados

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Varios

## Codigo SQL

```sql
WITH AgentesOrdenados AS (
    SELECT FECHA_CORTE,
           CLAVE_AGENTE,
           TIPO_DOCUMENTO,
           KEY_ID,
           CODIGO_LOCALIDAD,
           LOCALIDAD,
           FECHA_INGRESO,
           FECHA_RETIRO,
           CODIGO_CARGO,
           NOMBRE_CARGO,
           CLAVE_JEFE,
           PRIMER_NOMBRE,
           SEGUNDO_NOMBRE,
           PRIMER_APELLIDO,
           SEGUNDO_APELLIDO,
           FECHA_NACIMIENTO,
           EDAD,
           GENERO,
           ESTADO_CIVIL,
           SALARIO,
           ROW_NUMBER() OVER (PARTITION BY TIPO_DOCUMENTO, KEY_ID ORDER BY FECHA_CORTE DESC) AS rn
    FROM sb-ecosistemaanalitico-lago.seguros_bolivar.t_param_agentes_empleados
)
SELECT FECHA_CORTE,
       CLAVE_AGENTE,
       TIPO_DOCUMENTO,
       KEY_ID,
       CODIGO_LOCALIDAD,
       LOCALIDAD,
       FECHA_INGRESO,
       FECHA_RETIRO,
       CODIGO_CARGO,
       NOMBRE_CARGO,
       CLAVE_JEFE,
       PRIMER_NOMBRE,
       SEGUNDO_NOMBRE,
       PRIMER_APELLIDO,
       SEGUNDO_APELLIDO,
       FECHA_NACIMIENTO,
       EDAD,
       GENERO,
       ESTADO_CIVIL,
       SALARIO
FROM AgentesOrdenados
WHERE rn = 1
ORDER BY FECHA_CORTE DESC;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
