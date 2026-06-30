# Planta Y Medicos De Cofnfianza Ips

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Varios

## Codigo SQL

```sql



SELECT distinct
    TIPO_DOCUMENTO,
    KEY_ID,
    FUENTE,
    EMPRESA_RELACION,
    EMPRESA_CONTRATANTE_NOMBRE,
    CLASE_VINCULACION,
    DESCRIPCION_TIPO_RELACION,
    NOMBRE_DEPARTAMENTO_EMPRESA
  FROM `sb-ecosistemaanalitico-lago.talento_humano.t_colaboradores_activos`
  where FECHA_CIERRE = (
  SELECT MAX(FECHA_CIERRE)
  FROM `sb-ecosistemaanalitico-lago.talento_humano.t_colaboradores_activos`)
  and EMPRESA_RELACION in ("SALUD BOLIVAR IPS S.A.S","SALUD BOLIVAR EPS S.A.S")  or NOMBRE_DEPARTAMENTO_EMPRESA in ("AREA GESTION MEDICA DE SALUD BOL") --Planta y  Medicos de confianza

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
