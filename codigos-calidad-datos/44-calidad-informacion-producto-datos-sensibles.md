# Calidad de Datos: Información de Producto y Datos Sensibles

## Descripción
Auditoría de información sensible: verificar que datos financieros, pólizas y beneficiarios sean trazables y cumplan protecciones de privacidad.

---

## Código SQL - Oracle

```sql
SELECT 
  p.NUM_POL1,
  p.tdoc_tercero,
  p.nro_documto,
  p.PRIMA_ANUAL,
  p.COBERTURA,
  j.nombre AS tomador,
  COUNT(b.clave_beneficiario) AS cantidad_beneficiarios,
  CASE WHEN COUNT(b.clave_beneficiario) > 0 THEN 'TIENE_BENEFICIARIOS'
    ELSE 'SIN_BENEFICIARIOS' END AS status_beneficiarios
FROM A2000030 p
LEFT JOIN juridicos j ON j.tipdoc_codigo = p.tdoc_tercero AND j.numero_documento = p.nro_documto
LEFT JOIN beneficiarios b ON b.num_poliza = p.NUM_POL1
WHERE p.FECHA_VENC_POL >= SYSDATE
GROUP BY p.NUM_POL1, p.tdoc_tercero, p.nro_documto, p.PRIMA_ANUAL, p.COBERTURA, j.nombre;
```

---

**Categoría**: Calidad de Datos - Privacidad  
**Base de Datos**: Oracle  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29