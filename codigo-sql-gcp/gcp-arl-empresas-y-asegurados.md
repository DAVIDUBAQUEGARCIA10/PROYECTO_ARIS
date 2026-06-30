# Arl Empresas Y Asegurados

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Clientes

## Codigo SQL

```sql
SELECT DISTINCT 
    FECHA_INI_COBERTURA,
    TDOC_EMPRESA,
    NIT_EMPRESA,
    COD_CIA,
    COD_SECC,
    COD_RAMO,
    IDE_NIT,
    NIT,
    SALARIO,
    COD_EPS,
    COD_CARGO
FROM `sb-ecosistemaanalitico-lago.bienestar_seguros_bolivar.t_trabajadores_arl`
WHERE NIT_EMPRESA = "860002503"
AND ANIO_CORTE = (
    SELECT MAX(ANIO_CORTE)
    FROM `sb-ecosistemaanalitico-lago.bienestar_seguros_bolivar.t_trabajadores_arl`
)
AND MES_CORTE = (
    SELECT MAX(MES_CORTE)
    FROM `sb-ecosistemaanalitico-lago.bienestar_seguros_bolivar.t_trabajadores_arl`
    WHERE ANIO_CORTE = (
        SELECT MAX(ANIO_CORTE)
        FROM `sb-ecosistemaanalitico-lago.bienestar_seguros_bolivar.t_trabajadores_arl`
    )
);

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
