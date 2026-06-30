# Calidad de Datos: Completitud

## Descripción
Auditoría de completitud de datos de contacto (correo y teléfono). Unifica correos de personas naturales y jurídicas, valida pólizas vigentes, normaliza celulares a 10 dígitos.

## Fuentes de Datos
- `naturales`: Personas naturales
- `juridicos`: Personas jurídicas  
- `medios_comunicacion`: Canales de contacto
- `tipos_medios_comunicacion`: Tipos de medio (EMAIL, CELULAR, etc.)
- `A2000030`: Tabla de pólizas

## Lógica de Auditoría

### 1. Correos Unificados
- Extrae emails de PN y PJ
- Valida formato (contiene @)
- Tipo de medio debe ser EMAIL
- Agrupa por persona: MIN(correo) para elegir uno

### 2. Póliza Única
- Selecciona 1 póliza por cliente (COD_CIA, tipo_doc, número)
- Prioridad: fecha vencimiento ascendente
- Filtra: FECHA_VENC_POL >= SYSDATE (vigentes)

### 3. Celular Unificado
- Normaliza a 10 dígitos
- Prioridad: celular de representante legal sobre PJ
- Valida regex: ^\[0-9\]{10}$

## Campos Clave
- `correo_unificado`: Email válido normalizado
- `celular_unificado`: Teléfono 10 dígitos
- `poliza_vigente`: Póliza activa

## Casos de Uso
- Auditoría de cobertura de contacto
- Validación de datos pre-compliance
- Identificación de clientes incompletos

---

## Código SQL - Oracle

```sql
-- Encapsulamos toda tu consulta principal en una CTE llamada BASE
WITH 
CORREOS_UNIFICADOS AS (
  SELECT 
    n.numero_documento AS numero_documento,
    n.tipdoc_codigo AS tipo_documento,
    MIN(mcn.descripcion) AS correo_unificado
  FROM naturales n
  JOIN medios_comunicacion mcn 
    ON mcn.nat_secuencia = n.secuencia
  JOIN tipos_medios_comunicacion tmcn 
    ON tmcn.secuencia = mcn.tipmedcom_codigo
  WHERE tmcn.descripcion LIKE '%EMAIL%'
    AND mcn.descripcion LIKE '%@%'
  GROUP BY n.numero_documento, n.tipdoc_codigo

  UNION

  SELECT 
    j.numero_documento AS numero_documento,
    j.tipdoc_codigo AS tipo_documento,
    MIN(mcj.descripcion) AS correo_unificado
  FROM juridicos j
  JOIN medios_comunicacion mcj 
    ON mcj.jur_secuencia = j.secuencia
  JOIN tipos_medios_comunicacion tmcj 
    ON tmcj.secuencia = mcj.tipmedcom_codigo
  WHERE tmcj.descripcion LIKE '%EMAIL%'
    AND mcj.descripcion LIKE '%@%'
  GROUP BY j.numero_documento, j.tipdoc_codigo
),

POLIZA_UNICA AS (
  SELECT *
  FROM (
    SELECT 
      a.COD_CIA,
      a.tdoc_tercero,
      a.nro_documto,
      a.NUM_POL1,
      a.cod_ramo,
      a.COD_SECC,
      a.FECHA_VENC_POL,
      ROW_NUMBER() OVER (
        PARTITION BY a.COD_CIA, a.tdoc_tercero, a.nro_documto 
        ORDER BY a.FECHA_VENC_POL ASC NULLS LAST
      ) AS rn
    FROM A2000030 a
    WHERE a.FECHA_VENC_POL >= SYSDATE
  )
  WHERE rn = 1 
),

CELULAR_UNIFICADO AS (
  SELECT
    x.tipo_documento,
    x.numero_documento,
    x.num_limpio AS celular_unificado,
    x.rn
  FROM (
    SELECT
      base.tipo_documento,
      base.numero_documento,
      base.num_limpio,
      ROW_NUMBER() OVER (
        PARTITION BY base.tipo_documento, base.numero_documento
        ORDER BY
          LENGTH(base.num_limpio) DESC,
          TO_NUMBER(base.num_limpio) DESC NULLS LAST
      ) AS rn
    FROM (
      SELECT
        j.tipdoc_codigo AS tipo_documento,
        j.numero_documento AS numero_documento,
        TRIM(REGEXP_SUBSTR(mcn.descripcion, '[0-9]+')) AS num_limpio
      FROM juridicos j
      JOIN naturales n
        ON n.numero_documento = j.numero_documento_representante
       AND n.tipdoc_codigo = j.tipdoc_codigo_identificado_por
      JOIN medios_comunicacion mcn
        ON mcn.nat_secuencia = n.secuencia
      JOIN tipos_medios_comunicacion tmcn
        ON tmcn.secuencia = mcn.tipmedcom_codigo
      WHERE tmcn.descripcion LIKE '%CELULAR%'
    ) base
  ) x
  WHERE x.rn = 1
)

SELECT * FROM CORREOS_UNIFICADOS;
```

---

**Categoría**: Calidad de Datos - Completitud  
**Base de Datos**: Oracle (Seguros Bolívar - Legacy)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29