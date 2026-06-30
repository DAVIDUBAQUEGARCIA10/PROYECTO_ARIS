# Calidad de Datos: Colaboradores Activos - Talento Humano

## Descripción
Auditoría de colaboradores activos. Extrae información de empleados vigentes de Seguros Bolívar, sus pólizas activas y datos de contacto actualizado.

---

## Código SQL - Oracle

```sql
SELECT 
  e.numero_documento,
  e.tipdoc_codigo,
  e.nombre,
  e.estado,
  p.NUM_POL1,
  p.FECHA_VENC_POL,
  c.correo_unificado,
  c.celular_unificado
FROM empleados e
JOIN A2000030 p ON p.tdoc_tercero = e.tipdoc_codigo AND p.nro_documto = e.numero_documento
LEFT JOIN CORREOS_UNIFICADOS c ON c.numero_documento = e.numero_documento AND c.tipo_documento = e.tipdoc_codigo
WHERE e.estado = 'ACTIVO' AND p.FECHA_VENC_POL >= SYSDATE;
```

---

**Categoría**: Calidad de Datos - Talento Humano  
**Base de Datos**: Oracle  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29