# Calidad de Datos: Representante Legal - Clientes Vigentes

## Descripción
Extrae datos de representante legal de clientes jurídicos con productos vigentes, comparando información de ARIS contra Davivienda. Proporciona universo completo de clientes para auditoría de representación legal.

## Fuentes de Datos
- **Base Principal**: `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos`
- **Clientes Jurídicos**: `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_juridicos`
- **Davivienda**: `sb-ecosistemaanalitico-lago.davivienda.t_individuos_relacionados_pn_pj`

## Lógica de Consulta

### Base
Clientes jurídicos (NT/NE) con productos vigentes en Seguros Bolívar.

### Representante Legal Davivienda
Extrae individuos relacionados con `DESCRIPCION_RELACION = "REPRESENTANTE LEGAL"` y `ESTADO_DATO = "VIGENTE"`.
- Normaliza KEY_ID_CLIENTE a 9 dígitos
- ROW_NUMBER() para 1 representante por cliente
- Ordena por KEY_ID_RELACIONADO

### Resultado Final
LEFT JOIN entre base ARIS y representantes de Davivienda, mostrando:
- Representante registrado en ARIS (ARIS data)
- Representante según Davivienda (validación/enriquecimiento)

## Campos de Salida

| Campo | Descripción |
|-------|-------------|
| `TIPO_DOCUMENTO` | Tipo doc cliente |
| `KEY_ID` | NIT cliente |
| `NOMBRE` | Nombre cliente |
| `TIP_DOC_REPRESENTANTE_LEGAL` | Tipo doc representante (ARIS) |
| `KEY_ID_REPRESENTANTE_LEGAL` | ID representante (ARIS) |
| `TIP_DOC_REPRESENTANTE_LEGAL_DAV` | Tipo doc representante (Davivienda) |
| `KEY_ID_REPRESENTANTE_LEGAL_DAV` | ID representante (Davivienda) |

## Casos de Uso
- **Auditoría de representación**: Validar quién representa legalmente la empresa
- **Cumplimiento legal**: Confirmar datos ante autoridades
- **Validación cruzada**: Comparar ARIS vs Davivienda
- **Governance**: Identificar cambios de representación

---

## Código SQL

```sql
-- ============================================================
-- 1. DECLARAR VARIABLE
-- ============================================================
DECLARE periodo_max_primas DATE;

-- ============================================================
-- 2. ASIGNAR VALOR (FECHA MÁXIMA DE PROCESO)
-- ============================================================
SET periodo_max_primas = (
  SELECT MAX(DATE(FECHA_PROCESO))
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos`
);

-- ============================================================
-- 3. BASE PRINCIPAL (CLIENTES CON PRODUCTO VIGENTE)
-- ============================================================
WITH base AS (
  SELECT DISTINCT
    c.TIPO_DOCUMENTO,
    c.KEY_ID,
    SUBSTR(CAST(c.KEY_ID AS STRING), 1, 9) AS KEY_ID_SIN_DV,
    c.NOMBRE,
    c.TIP_DOC_REPRESENTANTE_LEGAL,
    c.KEY_ID_REPRESENTANTE_LEGAL
  FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos` p
  INNER JOIN `sb-ecosistemaanalitico-lago.seguros_bolivar.t_clientes_juridicos` c
    ON p.KEY_ID_TOMADOR = c.KEY_ID
   AND p.TIPO_DOCUMENTO_TOMADOR = c.TIPO_DOCUMENTO
  WHERE p.MCA_ANU_POLIZA != "S"
    AND DATE(p.FECHA_PROCESO) = periodo_max_primas
    AND DATE(p.FECHA_FIN_ENDOSO) >= CURRENT_DATE()
    AND DATE(p.FECHA_FIN_POLIZA) >= CURRENT_DATE()
    AND p.TIPO_DOCUMENTO_TOMADOR IN ("NT", "NE")
),

-- ============================================================
-- 4. REPRESENTANTE LEGAL DAVIVIENDA
--    1 REGISTRO POR CLIENTE
-- ============================================================
representante_legal AS (
  SELECT
    TIPO_DOCUMENTO_CLIENTE,
    KEY_ID_CLIENTE,
    TIPO_DOCUMENTO_RELACIONADO,
    KEY_ID_RELACIONADO
  FROM (
    SELECT
      TIPO_DOCUMENTO_CLIENTE,
      SUBSTR(CAST(KEY_ID_CLIENTE AS STRING), 1, 9) AS KEY_ID_CLIENTE,
      TIPO_DOCUMENTO_RELACIONADO,
      KEY_ID_RELACIONADO,

      ROW_NUMBER() OVER (
        PARTITION BY SUBSTR(CAST(KEY_ID_CLIENTE AS STRING), 1, 9)
        ORDER BY KEY_ID_RELACIONADO
      ) AS rn

    FROM `sb-ecosistemaanalitico-lago.davivienda.t_individuos_relacionados_pn_pj`
    WHERE ESTADO_DATO = "VIGENTE"
      AND DESCRIPCION_RELACION = "REPRESENTANTE LEGAL"
  )
  WHERE rn = 1
)

-- ============================================================
-- 5. RESULTADO FINAL
-- ============================================================
SELECT
  b.TIPO_DOCUMENTO,
  b.KEY_ID,
  b.KEY_ID_SIN_DV,
  b.NOMBRE,

  b.TIP_DOC_REPRESENTANTE_LEGAL,
  b.KEY_ID_REPRESENTANTE_LEGAL,

  r.TIPO_DOCUMENTO_RELACIONADO AS TIP_DOC_REPRESENTANTE_LEGAL_DAV,
  r.KEY_ID_RELACIONADO AS KEY_ID_REPRESENTANTE_LEGAL_DAV

FROM base b
LEFT JOIN representante_legal r
  ON b.KEY_ID_SIN_DV = r.KEY_ID_CLIENTE;
```

---

**Categoría**: Calidad de Datos - Validación de Representación Legal  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29