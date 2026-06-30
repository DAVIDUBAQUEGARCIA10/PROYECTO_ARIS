# Calidad de Datos: Beneficiario Final - Comparativa Revisoria (Faltantes)

## Descripción
Identifica clientes en revisoria fiscal que **NO tienen beneficiarios finales identificados** en el registro de contactos. Indicador de datos incompletos o faltantes en estructura corporativa.

## Diferencia con Archivo A
- **Archivo A**: Clientes con beneficiarios identificados (COUNT de coincidencias)
- **Archivo B**: Clientes SIN beneficiarios identificados (Gap analysis)

## Fuentes de Datos
- **Revisoria Fiscal**: `dc_ext_revisoria_fiscal`
- **Contactos**: `contactos` (tabla interna)

## Lógica de Consulta

### Criterio Clave
```sql
WHERE NUM_DOCUMENTO_CONTACTO IS NULL
```
Retorna registros donde el LEFT JOIN no encontró coincidencia en contactos. Indica que hay cliente en revisoria pero no hay beneficiario registrado.

## Campos de Salida
- `Cod_Tipo_Doc`: Tipo documento cliente
- `Num_Id`: NIT cliente
- `NUM_DOCUMENTO_CONTACTO`: NULL (no hay coincidencia)
- `NOMBRE_CONTACTO`: NULL (no hay coincidencia)

## Casos de Uso
- **Auditoría de gaps**: Estructuras sin beneficiario identificado
- **Plan de remedición**: Clientes que necesitan actualización de RUES
- **Cumplimiento**: Detectar información incompleta
- **Validación de calidad**: Monitoreo mensual de cobertura

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

SELECT *
FROM revisoria r
LEFT JOIN contactos_filtrados c
  ON r.Cod_Tipo_Doc = c.TIPDOC_CODIGO
 AND r.Num_Id = c.NUMERO_DOCUMENTO
 WHERE NUM_DOCUMENTO_CONTACTO IS NULL;
```

---

**Categoría**: Calidad de Datos - Auditoría de Gaps  
**Base de Datos**: Interna (Seguros Bolívar)  
**Versión**: 1.0  
**Última Actualización**: 2026-06-29