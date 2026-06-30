# Calidad de Datos: Beneficiario Final - Relacionados en Davivienda

## Descripción
Extrae información de individuos relacionados como socios y accionistas desde Davivienda. Utiliza tabla `t_individuos_relacionados_pn_pj` para identificar beneficiarios finales con estados vigentes. Proporciona vista de estructura de propiedad según Davivienda.

## Fuentes de Datos
- **Davivienda**: `sb-ecosistemaanalitico-lago.davivienda.t_individuos_relacionados_pn_pj`

## Lógica de Consulta

### Filtros Clave
- `ESTADO_DATO = "VIGENTE"`: Solo relacionados vigentes
- `DESCRIPCION_RELACION IN ("SOCIO", "ACCIONISTA")`: Solo beneficiarios finales
- `KEY_ID_CLIENTE`: NIT de la empresa

### Normalización
- KEY_ID_CLIENTE: Normalizado a 9 dígitos (sin DV)
- ROW_NUMBER() por cliente para seleccionar 1 beneficiario por NIT
- Orden por KEY_ID_RELACIONADO (consistencia)

## Campos de Salida

| Campo | Descripción |
|-------|-------------|
| `TIPO_DOCUMENTO_CLIENTE` | Tipo doc del cliente (empresa) |
| `KEY_ID_CLIENTE` | NIT cliente normalizado a 9 dígitos |
| `TIPO_DOCUMENTO_RELACIONADO` | Tipo doc del beneficiario |
| `KEY_ID_RELACIONADO` | Identificación del beneficiario |

## Casos de Uso
- **Estructura de propiedad**: Mapeo de beneficiarios según Davivienda
- **Auditoría KYC**: Validación de identificación de beneficiarios
- **Due diligence**: Análisis de estructura corporativa
- **Cumplimiento**: Validación de PEPS y listas restrictivas

---

## Código SQL

```sql
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
    AND DESCRIPCION_RELACION IN ("SOCIO", "ACCIONISTA")
)
WHERE rn = 1;
```

---

**Categoría**: Calidad de Datos - Validación de Estructura Corporativa  
**Base de Datos**: BigQuery (`sb-ecosistemaanalitico-lago.davivienda`)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29