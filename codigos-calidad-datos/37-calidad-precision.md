# Calidad de Datos: Precisión

## Descripción
Validación de precisión: verifica exactitud y consistencia de datos. Celulares de representantes legales deben ser numéricos y de 10 dígitos, correos deben tener formato válido.

---

## Código SQL - Oracle

```sql
WITH CELULAR_PRECISION AS (
  SELECT tipo_documento, numero_documento,
    CASE 
      WHEN LENGTH(TRIM(REGEXP_SUBSTR(mcn.descripcion, '[0-9]+'))) = 10 
        AND REGEXP_LIKE(TRIM(REGEXP_SUBSTR(mcn.descripcion, '[0-9]+')), '^[0-9]{10}$')
      THEN 'VÁLIDO'
      ELSE 'INVÁLIDO'
    END AS precision_celular
  FROM juridicos j
  JOIN naturales n ON n.numero_documento = j.numero_documento_representante
  JOIN medios_comunicacion mcn ON mcn.nat_secuencia = n.secuencia
),

CORREO_PRECISION AS (
  SELECT numero_documento, tipdoc_codigo,
    CASE
      WHEN descripcion LIKE '%@%' AND descripcion LIKE '%.%'
      THEN 'VÁLIDO'
      ELSE 'INVÁLIDO'
    END AS precision_correo
  FROM medios_comunicacion mcn
  JOIN naturales n ON mcn.nat_secuencia = n.secuencia
  WHERE descripcion LIKE '%@%'
)

SELECT * FROM CELULAR_PRECISION WHERE precision_celular = 'INVÁLIDO';
```

---

**Categoría**: Calidad de Datos - Precisión  
**Base de Datos**: Oracle  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29