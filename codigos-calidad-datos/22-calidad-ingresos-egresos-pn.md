# Calidad de Datos: Ingresos y Egresos - PN Davivienda

## Descripción
Extrae ingresos y egresos de personas naturales desde Davivienda. Análisis de depuración de datos financieros de PN con ROW_NUMBER para seleccionar registro más reciente.

---

## Código SQL

```sql
DECLARE periodo_max_davivienda DATE;

SET periodo_max_davivienda = (
    SELECT MAX(DATE(FECHA_PROCESO))
    FROM `sb-ecosistemaanalitico-lago.davivienda.t_clientes_pn`
);

WITH davivienda_pn AS (
    SELECT KEY_ID, INGRESO_2, FECHA_INGRESO_2, FECHA_PROCESO
    FROM (
        SELECT KEY_ID, INGRESO_2, FECHA_INGRESO_2, FECHA_PROCESO,
          ROW_NUMBER() OVER (PARTITION BY KEY_ID ORDER BY FECHA_INGRESO_2 DESC) AS rn
        FROM `sb-ecosistemaanalitico-lago.davivienda.t_clientes_pn`
        WHERE DATE(FECHA_PROCESO) = periodo_max_davivienda
    )
    WHERE rn = 1
)

SELECT * FROM davivienda_pn;
```

---

**Categoría**: Calidad de Datos - Ingresos/Egresos PN  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago.davivienda`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29