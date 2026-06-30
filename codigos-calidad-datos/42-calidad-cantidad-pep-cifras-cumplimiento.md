# Calidad de Datos: Cantidad PEP - Cifras Oficial de Cumplimiento

## Descripción
Reporte de PEPS (Personas Expuestas Políticamente). Contabiliza cantidad de clientes identificados como PEP, estado de listas restrictivas, y estado de cumplimiento normativo.

---

## Código SQL - Oracle

```sql
SELECT 
  COUNT(DISTINCT CONCAT(tipdoc_codigo, '-', numero_documento)) AS total_clientes,
  SUM(CASE WHEN tiene_flag_pep = 'S' THEN 1 ELSE 0 END) AS cantidad_pep,
  SUM(CASE WHEN lista_restrictiva != 'NINGUNA' THEN 1 ELSE 0 END) AS en_lista_restrictiva,
  SUM(CASE WHEN estado_cumplimiento = 'CUMPLE' THEN 1 ELSE 0 END) AS cumplen_normativa,
  ROUND((SUM(CASE WHEN estado_cumplimiento = 'CUMPLE' THEN 1 ELSE 0 END) / COUNT(*)) * 100, 2) AS porcentaje_cumplimiento
FROM clientes_cumplimiento
WHERE fecha_validacion >= TRUNC(SYSDATE, 'YEAR');
```

---

**Categoría**: Calidad de Datos - Cumplimiento Normativo  
**Base de Datos**: Oracle  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29