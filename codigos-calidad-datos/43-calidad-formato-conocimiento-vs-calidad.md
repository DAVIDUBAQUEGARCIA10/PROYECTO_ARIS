# Calidad de Datos: Formato de Conocimiento vs Calidad de Datos

## Descripción
Comparación entre formato de conocimiento registrado (KYC, Due Diligence) y calidad real de datos en sistemas. Identifica brechas entre lo documentado y lo registrado.

---

## Código SQL - Oracle

```sql
WITH FORMATO_KYC AS (
  SELECT 
    numero_documento, tipdoc_codigo,
    COUNT(DISTINCT campo_kyc) AS campos_kyc_documentados,
    SUM(CASE WHEN dato_kyc IS NOT NULL THEN 1 ELSE 0 END) AS campos_llenos
  FROM kyc_documentos
  GROUP BY numero_documento, tipdoc_codigo
),

CALIDAD_SISTEMA AS (
  SELECT 
    numero_documento, tipdoc_codigo,
    COUNT(DISTINCT CASE WHEN descripcion IS NOT NULL THEN tipmedcom_codigo END) AS medios_registro,
    SUM(CASE WHEN descripcion IS NOT NULL THEN 1 ELSE 0 END) AS datos_activos
  FROM medios_comunicacion
  GROUP BY numero_documento, tipdoc_codigo
)

SELECT 
  k.numero_documento, k.tipdoc_codigo,
  k.campos_kyc_documentados,
  k.campos_llenos,
  c.medios_registro,
  c.datos_activos,
  CASE WHEN k.campos_kyc_documentados = c.medios_registro THEN 'CONSISTENTE'
    ELSE 'INCONSISTENTE' END AS evaluacion
FROM FORMATO_KYC k
FULL OUTER JOIN CALIDAD_SISTEMA c 
  ON k.numero_documento = c.numero_documento AND k.tipdoc_codigo = c.tipdoc_codigo;
```

---

**Categoría**: Calidad de Datos - Conocimiento de Cliente  
**Base de Datos**: Oracle  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29