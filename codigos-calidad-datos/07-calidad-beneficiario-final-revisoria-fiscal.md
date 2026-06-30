# Calidad de Datos: Beneficiario Final - Análisis Revisoria Fiscal

## Descripción
Análisis de beneficiarios finales identificados en revisoria fiscal. Extrae datos de clientes que aparecen en tabla de revisoria fiscal (NT/NE) y los compara con contactos que tienen porcentaje de participación registrado. Útil para auditoría de estructuras de propiedad.

## Fuentes de Datos
- **Revisoria Fiscal**: `dc_ext_revisoria_fiscal`
- **Contactos**: `contactos` (tabla interna)
- **Juridicos**: `juridicos` (tabla interna)

## Lógica de Consulta

### 1. Base Revisoria Fiscal
Extrae clientes (NT/NE) que tienen registros en revisoria fiscal.

### 2. Contactos Filtrados
Selecciona contactos que cumplan:
- Tienen porcentaje de participación registrado
- Están marcados como activos (`MARCA_ACTIVO = 'A'`)
- Participación entre 1% y 100%
- Se toma el registro más reciente (MAX SECUENCIA)

### 3. Resultado
LEFT JOIN entre revisoria fiscal y contactos. **Retorna COUNT(*)** de registros con beneficiarios identificados.

## Campos Clave
- `Cod_Tipo_Doc`: Tipo de documento (NT/NE)
- `Num_Id`: Número de identificación
- `NUM_DOCUMENTO_CONTACTO`: Documento del beneficiario
- `NOMBRE_CONTACTO`: Nombre del beneficiario
- `PORCENTAJE_PARTICIPACION`: % de participación accionaria

## Casos de Uso
- **Auditoría de estructura**: Verificar beneficiarios finales identificados
- **Cumplimiento**: Validar RUES y estructura corporativa
- **Due diligence**: Análisis previo a transacciones
- **Monitoreo de riesgo**: Identificar cambios de propiedad

---

## Código SQL

```sql
WITH revisoria AS (
  SELECT DISTINCT 
    exrf_cod_tip_doc AS Cod_Tipo_Doc,
    exrf_num_id_cliente AS Num_Id
  FROM dc_ext_revisoria_fiscal
  WHERE exrf_cod_tip_doc IN ('NT','NE')
),

contactos_filtrados AS (
  SELECT
    NUMERO_DOCUMENTO,
    TIPDOC_CODIGO,
    TIPDOC_CODIGO_CONTACTO,
    NUM_DOCUMENTO_CONTACTO,
    NOMBRE_CONTACTO
  FROM (
    SELECT  
      j.NUMERO_DOCUMENTO,
      j.TIPDOC_CODIGO,
      c."TIPDOC_CODIGO" AS TIPDOC_CODIGO_CONTACTO,    
      c.NUMERO_DOCUMENTO AS NUM_DOCUMENTO_CONTACTO,
      c."NOMBRE_CONTACTO",

      ROW_NUMBER() OVER (
        PARTITION BY j.NUMERO_DOCUMENTO, j.TIPDOC_CODIGO
        ORDER BY c."SECUENCIA" DESC
      ) AS rn

    FROM contactos c
    JOIN juridicos j 
      ON j."SECUENCIA" = c."JUR_SECUENCIA"

    WHERE c."PORCENTAJE_PARTICIPACION" IS NOT NULL
      AND c."SECUENCIA" = (
          SELECT MAX(c1."SECUENCIA")
          FROM contactos c1
          WHERE c1."JUR_SECUENCIA" = c."JUR_SECUENCIA"
            AND c1."NUMERO_DOCUMENTO" = c."NUMERO_DOCUMENTO"
            AND c1."TIPDOC_CODIGO" = c."TIPDOC_CODIGO"
      )
    AND c."MARCA_ACTIVO" = 'A'
      AND c."PORCENTAJE_PARTICIPACION" > 1
      AND c."PORCENTAJE_PARTICIPACION" <= 100
  )
  WHERE rn = 1 
)

SELECT COUNT (*)
FROM revisoria r
LEFT JOIN contactos_filtrados c
  ON r.Cod_Tipo_Doc = c.TIPDOC_CODIGO
 AND r.Num_Id = c.NUMERO_DOCUMENTO
 WHERE NUM_DOCUMENTO_CONTACTO IS NOT NULL;
```

---

**Categoría**: Calidad de Datos - Validación de Estructura Corporativa  
**Base de Datos**: Interna (Seguros Bolívar)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29