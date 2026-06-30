# Consulta Contrapartes 360

## Descripcion
Script BigQuery que consolida informacion de todas las contrapartes (clientes, tomadores, asegurados, beneficiarios, proveedores, funcionarios, etc.) del grupo Seguros Bolivar en una vista unica de 360 grados. Integra datos de multiples fuentes y productos para proporcionar una vista integral de cada persona asociada a la organizacion.

## Tipo
Base de datos: BigQuery
Categoria: Consultas de Inteligencia de Negocio
Tabla de destino: sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.consulta_contrapartes_360

## Descripcion Detallada

### Proposito
Crear una tabla consolidada que unifique informacion de todas las contrapartes del grupo Seguros Bolivar, incluyendo:
- Clientes de capitalizadora
- Tomadores de polizas
- Asegurados
- Beneficiarios
- Empresas ARL
- Trabajadores ARL
- Clientes Securitas
- Funcionarios del grupo
- Proveedores
- Beneficiarios finales
- Clientes de 100 Cuadras
- Clientes de Facilities

### Fuentes de Datos
- Capitalizadora: t_clientes_capitalizadora
- Seguros Bolivar: t_polizas_riesgos_endosos, t_beneficiarios_porcentaje, t_terceros_restringidos_con_davivienda, t_registros_transacciones_formatos_sarlaft, t_beneficiario_final
- El Libertador: t_solicitudes
- Affinity: t_clientes_vigentes_affinity_bs
- Bienestar: t_trabajadores_arl, t_securitas_pens_beneficiarios
- People Analytics: v_colaboradores_activos
- Cumplimiento Normativo: proveedores_grupo_bolivar, score_riesgo_prod
- 100 Cuadras: t_compras_ciencuadras, t_leads_cien_cuadras
- Operaciones: t_sedes_clientes_facilities

### Campos de Salida
- CODIGO_COMPANIA: Codigo de la compania (1=Bolivar, 2=Securitas, 75=El Libertador, 100=Affinity, 0=Otros)
- TIPO_DOCUMENTO_TOMADOR: Tipo de identificacion
- KEY_ID_TOMADOR: Numero de identificacion
- CODIGO_RAMO_EMISION: Codigo de ramo (si aplica)
- CODIGO_PRODUCTO: Codigo de producto (si aplica)
- ESTADO: Estado de la relacion (Vigente, Beneficiario Activo, etc.)
- DESCRIPCION: Descripcion del estado o relacion
- ROL: Rol de la persona en la relacion (TITULAR, TOMADOR, ASEGURADO, BENEFICIARIO, etc.)
- PRODUCTO: Identificador del producto o poliza (si aplica)
- NOMBRE_CANAL: Canal o fuente de la relacion
- AREA_RESTRICCION_DAV: Area de restriccion segun Davivienda (LEFT JOIN)
- DESCRIPCION_DAV: Descripcion de restriccion Davivienda
- CLIENTE_PEP_DAV: Indicador de cliente PEP segun Davivienda
- CODIGO_RESTRICCION: Codigo de restriccion
- DESCRIPCION_RESTRICCION: Descripcion de restriccion
- FECHA_TRANSACCION: Fecha de ultima transaccion
- USUARIO_TRANSACCION: Usuario de ultima transaccion
- FORMULARIO_TRANSACCION: Formulario de transaccion
- RESULTADO_TRANSACCION: Resultado de transaccion
- Nivel_de_Riesgo: Nivel de riesgo del cliente

### Logica Clave
1. Usa multiples UNION ALL para consolidar datos de diferentes fuentes
2. Normaliza datos a estructura comun (CODIGO_COMPANIA, TIPO_DOCUMENTO, KEY_ID)
3. Aplica filtros de vigencia segun cada fuente
4. Realiza LEFT JOINs con tablas de riesgo, restricciones y transacciones
5. Deduplica resultados usando ROW_NUMBER() cuando es necesario

---

## Codigo SQL

```sql
CREATE OR REPLACE TABLE `sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.consulta_contrapartes_360` AS 

WITH polizas_consolidadas AS (

    -- Capitalizacion
    SELECT DISTINCT
        1 AS CODIGO_COMPANIA,
        tipo_documento AS TIPO_DOCUMENTO_TOMADOR,
        CAST(key_id AS STRING) AS KEY_ID_TOMADOR,
        801 AS CODIGO_RAMO_EMISION,
        51 AS CODIGO_PRODUCTO,
        "Vigente" as ESTADO,
        "No aplica" AS DESCRIPCION,
        'TITULAR' AS ROL,
        titulo as PRODUCTO,
        'CAPITALIZADORA' AS NOMBRE_CANAL

    FROM `sb-ecosistemaanalitico-lago.capitalizadora.t_clientes_capitalizadora`
    WHERE DATE(fecha_corte) = (
        SELECT MAX(DATE(fecha_corte))
        FROM `sb-ecosistemaanalitico-lago.capitalizadora.t_clientes_capitalizadora`
    )

    UNION ALL

    -- Tomador de polizas vigentes
    SELECT DISTINCT
        CODIGO_COMPANIA,
        TIPO_DOCUMENTO_TOMADOR,
        CAST(KEY_ID_TOMADOR AS STRING) AS KEY_ID_TOMADOR,
        CODIGO_RAMO_EMISION,
        CODIGO_PRODUCTO,
        "Vigente" as ESTADO,
        "No aplica" AS DESCRIPCION,
        'TOMADOR' AS ROL,
        NUMERO_POLIZA as PRODUCTO,
        NOMBRE_CANAL
    FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos`
    WHERE DATE(FECHA_FIN_POLIZA) >= CURRENT_DATE()
      AND MCA_ANU_POLIZA != 'S'
    QUALIFY FECHA_PROCESO = MAX(FECHA_PROCESO) OVER ()

    UNION ALL

    -- Asegurado de polizas vigentes
    SELECT DISTINCT
        CODIGO_COMPANIA,
        TIPO_DOCUMENTO_ASEGURADO AS TIPO_DOCUMENTO_TOMADOR,
        CAST(KEY_ID_ASEGURADO AS STRING) AS KEY_ID_TOMADOR,
        CODIGO_RAMO_EMISION,
        CODIGO_PRODUCTO,
        "Vigente" as ESTADO,
        "No aplica" AS DESCRIPCION,
        'ASEGURADO' AS ROL,
        NUMERO_POLIZA as PRODUCTO,
        NOMBRE_CANAL
    FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_riesgos_endosos`
    WHERE DATE(FECHA_FIN_POLIZA) >= CURRENT_DATE()
      AND MCA_ANU_POLIZA != 'S'

    UNION ALL

    -- Beneficiario de polizas vigentes
    SELECT DISTINCT
        CODIGO_COMPANIA,
        TIPO_DOCUMENTO_BENEFICIARIO AS TIPO_DOCUMENTO_TOMADOR,
        CAST(KEY_ID_BENEFICIARIO AS STRING) AS KEY_ID_TOMADOR,
        CODIGO_RAMO_EMISION,
        CODIGO_PRODUCTO,
        "Vigente" as ESTADO,
        "No aplica" AS DESCRIPCION,
        'BENEFICIARIO' AS ROL,
        NUMERO_POLIZA as PRODUCTO,
        NOMBRE_CANAL
    FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_polizas_tom_ase_ben_vigentes`
    WHERE DATE(FECHA_FIN_POLIZA) >= CURRENT_DATE()

    UNION ALL

    -- El Libertador / Inmobiliaria
    SELECT
        75 AS CODIGO_COMPANIA,
        tipo_documento AS TIPO_DOCUMENTO_TOMADOR,
        CAST(key_id AS STRING) AS KEY_ID_TOMADOR,
        0 AS CODIGO_RAMO_EMISION,
        0 AS CODIGO_PRODUCTO,
        "Producto vigente" as ESTADO,
        "No aplica" AS DESCRIPCION,
        'EL LIBERTADOR' AS ROL,
        numero_poliza as PRODUCTO,
        'El libertador' AS NOMBRE_CANAL
    FROM `sb-ecosistemaanalitico-lago.el_libertador.t_solicitudes`
    WHERE FECHA_CORTE >= '2026-04-01'
    QUALIFY ROW_NUMBER() OVER (
        PARTITION BY tipo_documento, key_id
        ORDER BY FECHA_CORTE DESC
    ) = 1

    UNION ALL

    -- Affinity tomador
    SELECT
        100 AS CODIGO_COMPANIA,
        TIPO_DOCUMENTO_TOMADOR AS TIPO_DOCUMENTO_TOMADOR,
        CAST(KEY_ID_TOMADOR AS STRING) AS KEY_ID_TOMADOR,
        CODIGO_RAMO_CONTABLE AS CODIGO_RAMO_EMISION,
        CODIGO_PRODUCTO AS CODIGO_PRODUCTO,
        "Vigente" as ESTADO,
        "No aplica" AS DESCRIPCION,
        'AFFINITYS TOMADOR' AS ROL,
        NUMERO_POLIZA as PRODUCTO,
        CAST(CODIGO_LOCALIDAD AS STRING) AS NOMBRE_CANAL
    FROM `sb-ecosistemaanalitico-lago.linea_negocio_bancaseguros_prod.t_clientes_vigentes_affinity_bs`
    WHERE FECHA_CORTE = (
        SELECT MAX(FECHA_CORTE)
        FROM `sb-ecosistemaanalitico-lago.linea_negocio_bancaseguros_prod.t_clientes_vigentes_affinity_bs`
    )

    UNION ALL

    -- Affinity asegurado
    SELECT
        100 AS CODIGO_COMPANIA,
        TIPO_DOCUMENTO_ASEGURADO AS TIPO_DOCUMENTO_TOMADOR,
        CAST(KEY_ID_ASEGURADO AS STRING) AS KEY_ID_TOMADOR,
        CODIGO_RAMO_CONTABLE AS CODIGO_RAMO_EMISION,
        CODIGO_PRODUCTO AS CODIGO_PRODUCTO,
        "Vigente" as ESTADO,
        "No aplica" AS DESCRIPCION,
        'AFFINITYS ASEGURADO' AS ROL,
        NUMERO_POLIZA as PRODUCTO,
        CAST(CODIGO_LOCALIDAD AS STRING) AS NOMBRE_CANAL
    FROM `sb-ecosistemaanalitico-lago.linea_negocio_bancaseguros_prod.t_clientes_vigentes_affinity_bs`
    WHERE FECHA_CORTE = (
        SELECT MAX(FECHA_CORTE)
        FROM `sb-ecosistemaanalitico-lago.linea_negocio_bancaseguros_prod.t_clientes_vigentes_affinity_bs`
    )

    UNION ALL

    -- Benefiarios de Polizas    

    SELECT
        CODIGO_COMPANIA AS CODIGO_COMPANIA,
        TIPO_DOCUMENTO_BENEFICIARIO AS TIPO_DOCUMENTO_TOMADOR,
        CAST(KEY_ID_BENEFICIARIO AS STRING) AS KEY_ID_TOMADOR,
        CODIGO_RAMO_EMISION AS CODIGO_RAMO_EMISION,
        CODIGO_PRODUCTO AS CODIGO_PRODUCTO,
        "Beneficiario Activo" as ESTADO,
        "No aplica" AS DESCRIPCION,
        'BENEFICIARIO DE SEGUROS' AS ROL,
        0 as PRODUCTO,
        CAST(TIPO_BENEFICIARIO AS STRING) AS NOMBRE_CANAL
    FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_beneficiarios_porcentaje`

    UNION ALL

    -- Arl empresas  

    SELECT DISTINCT 
        COD_CIA AS CODIGO_COMPANIA,
        TDOC_EMPRESA AS TIPO_DOCUMENTO_TOMADOR,
        NIT_EMPRESA AS KEY_ID_TOMADOR,
        COD_SECC AS CODIGO_RAMO_EMISION,
        COD_RAMO AS CODIGO_PRODUCTO,
        "Vigente" as ESTADO,
        "No aplica" AS DESCRIPCION,
        "ARL EMPRESAS" AS ROL,
        NUM_POL_CLI as PRODUCTO,
        "ARL EMPRESAS" AS NOMBRE_CANAL
    FROM `sb-ecosistemaanalitico-lago.bienestar_seguros_bolivar.t_trabajadores_arl`
    WHERE ANIO_CORTE = (
        SELECT MAX(ANIO_CORTE)
        FROM `sb-ecosistemaanalitico-lago.bienestar_seguros_bolivar.t_trabajadores_arl`
    )
    AND MES_CORTE = (
        SELECT MAX(MES_CORTE)
        FROM `sb-ecosistemaanalitico-lago.bienestar_seguros_bolivar.t_trabajadores_arl`
        WHERE ANIO_CORTE = (
            SELECT MAX(ANIO_CORTE)
            FROM `sb-ecosistemaanalitico-lago.bienestar_seguros_bolivar.t_trabajadores_arl`
        )
    )

    UNION ALL

    -- Arl trabajadores  

    SELECT DISTINCT 
        COD_CIA AS CODIGO_COMPANIA,
        IDE_NIT AS TIPO_DOCUMENTO_TOMADOR,
        NIT AS KEY_ID_TOMADOR,
        COD_SECC AS CODIGO_RAMO_EMISION,
        COD_RAMO AS CODIGO_PRODUCTO,
        "Vigente" as ESTADO,
        "No aplica" AS DESCRIPCION,
        "ARL - TRABAJADORES" AS ROL,
        NUM_POL_CLI as PRODUCTO,
        "ARL - TRABAJADORES" AS NOMBRE_CANAL
    FROM `sb-ecosistemaanalitico-lago.bienestar_seguros_bolivar.t_trabajadores_arl`
    WHERE ANIO_CORTE = (
        SELECT MAX(ANIO_CORTE)
        FROM `sb-ecosistemaanalitico-lago.bienestar_seguros_bolivar.t_trabajadores_arl`
    )
    AND MES_CORTE = (
        SELECT MAX(MES_CORTE)
        FROM `sb-ecosistemaanalitico-lago.bienestar_seguros_bolivar.t_trabajadores_arl`
        WHERE ANIO_CORTE = (
            SELECT MAX(ANIO_CORTE)
            FROM `sb-ecosistemaanalitico-lago.bienestar_seguros_bolivar.t_trabajadores_arl`
        )
    )

    UNION ALL 

    -- Securitas

    SELECT DISTINCT
        2 AS CODIGO_COMPANIA,
        ABREVIATURA AS TIPO_DOCUMENTO_TOMADOR,
        KEY_ID AS KEY_ID_TOMADOR,
        CASE
            WHEN LENGTH(CAST(NUMERO_POLIZA AS STRING)) >= 5 THEN 72
            WHEN LENGTH(CAST(NUMERO_POLIZA AS STRING)) < 5 THEN 72
        END AS CODIGO_RAMO_EMISION,
        CASE
            WHEN LENGTH(CAST(NUMERO_POLIZA AS STRING)) >= 5 THEN 272
            WHEN LENGTH(CAST(NUMERO_POLIZA AS STRING)) < 5 THEN 272
        END AS CODIGO_PRODUCTO,
        "Vigente" as ESTADO,
        "No aplica" AS DESCRIPCION,
        CAST(
            CASE
                WHEN LENGTH(CAST(NUMERO_POLIZA AS STRING)) >= 5 THEN 5
                WHEN LENGTH(CAST(NUMERO_POLIZA AS STRING)) < 5 THEN 1
            END AS STRING
        ) AS ROL,
        NUMERO_POLIZA as PRODUCTO,
        'Securitas' AS NOMBRE_CANAL
    FROM `sb-ecosistemaanalitico-lago.bienestar_prod.t_securitas_pens_beneficiarios`

    UNION ALL

    -- Funcionarios del grupo

    SELECT DISTINCT
        0 AS CODIGO_COMPANIA, 
        TIPO_DOCUMENTO AS TIPO_DOCUMENTO_TOMADOR,
        KEY_ID AS KEY_ID_TOMADOR,
        0  AS CODIGO_RAMO_EMISION,
        0  AS CODIGO_PRODUCTO,
        "Vigente" as ESTADO,
        "No aplica" AS DESCRIPCION,
        EMPRESA_RELACION AS ROL,
        0 as PRODUCTO,
        'FUNCIONARIO' AS NOMBRE_CANAL
    FROM `sb-ecosistemaanalitico-lago.people_analytics_prod.v_colaboradores_activos`
    WHERE FECHA_CIERRE = (
        SELECT MAX(FECHA_CIERRE)
        FROM `sb-ecosistemaanalitico-lago.people_analytics_prod.v_colaboradores_activos`
    )

    UNION ALL

    -- Proveedores vigientes 
    SELECT DISTINCT
        0 AS CODIGO_COMPANIA, 
        TIPO_DOCUMENTO AS TIPO_DOCUMENTO_TOMADOR,
        KEY_ID AS KEY_ID_TOMADOR,
        0  AS CODIGO_RAMO_EMISION,
        0  AS CODIGO_PRODUCTO,
        ESTADO as ESTADO,
        "No aplica" AS DESCRIPCION,
        PROVEEDOR_DEL_GRUPO AS ROL,
        0 as PRODUCTO,
        'Proveedor' || ESTADO AS NOMBRE_CANAL
    FROM sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.proveedores_grupo_bolivar

    UNION ALL

    ---Beneficiario final 

    SELECT DISTINCT
        0 AS CODIGO_COMPANIA, 
        TIPO_DOCUMENTO_SOCIEDAD_ACCIONISTA as TIPO_DOCUMENTO_TOMADOR ,
        KEY_ID AS KEY_ID_TOMADOR,
        0  AS CODIGO_RAMO_EMISION,
        0  AS CODIGO_PRODUCTO,
        "Vigente" as ESTADO,
        "No aplica" AS DESCRIPCION,
        CONCAT(
            COALESCE(CARGO_DEL_CONTACTO, ''),
            ' vinculado con la empresa ',
            COALESCE(CAST(KEY_ID AS STRING), '')
        ) AS ROL,
        0 as PRODUCTO,
        'Beneficiario final' AS NOMBRE_CANAL
    FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_beneficiario_final`
    WHERE MARCA_REGISTRO_ACTIVO = 'A'



    UNION ALL
   ----100 Cuadras 


   SELECT
   0 AS CODIGO_COMPANIA, 
  CASE
    WHEN LENGTH(CAST(key_id AS STRING)) > 10 THEN 'CC'
    WHEN LENGTH(CAST(key_id AS STRING)) = 9 THEN 'NT'
    ELSE NULL
  END AS TIPO_DOCUMENTO_TOMADOR,
    key_id as KEY_ID_TOMADOR,
    0  AS CODIGO_RAMO_EMISION,
    0  AS CODIGO_PRODUCTO,
    "Vigente" as ESTADO,
    "Servicios 100 Cuadras" AS DESCRIPCION,
    nombre_producto AS ROL,
    0 as PRODUCTO,
    '100 CUADRAS' AS NOMBRE_CANAL

FROM (
  SELECT
    nombre_producto,
    key_id,
    fecha_creacion,
    ROW_NUMBER() OVER (
      PARTITION BY key_id
      ORDER BY fecha_creacion DESC
    ) AS rn
  FROM `sb-ecosistemaanalitico-lago.cien_cuadras.t_compras_ciencuadras`
)
WHERE rn = 1 AND key_id IS NOT NULL


---- 100 cuadras

UNION ALL

   SELECT
   0 AS CODIGO_COMPANIA, 
    TIPO_DOCUMENTO as TIPO_DOCUMENTO_TOMADOR,
    key_id as KEY_ID_TOMADOR,
    0  AS CODIGO_RAMO_EMISION,
    0  AS CODIGO_PRODUCTO,
    "Vigente" as ESTADO,
    "Pagina 100 Cuadras" AS DESCRIPCION,
    FUENTE AS ROL,
    0 as PRODUCTO,
    '100 CUADRAS' AS NOMBRE_CANAL

FROM (
  SELECT
    FUENTE,
    key_id,
    TIPO_DOCUMENTO,
    FECHA_PROCESO,
    ROW_NUMBER() OVER (
      PARTITION BY key_id
      ORDER BY FECHA_PROCESO DESC
    ) AS rn
  FROM `sb-ecosistemaanalitico-lago.cien_cuadras.t_leads_cien_cuadras`
)
WHERE rn = 1 AND key_id IS NOT NULL


    UNION ALL

   
-- Facilities / sedes clientes

SELECT DISTINCT
    0 AS CODIGO_COMPANIA, 
    'NT' AS TIPO_DOCUMENTO_TOMADOR,

    -- NIT extraido de CODIGO_RECURSIVA
    SPLIT(CODIGO_RECURSIVA, '-')[SAFE_OFFSET(0)] AS KEY_ID_TOMADOR,

    0 AS CODIGO_RAMO_EMISION,
    0 AS CODIGO_PRODUCTO,

    'Vigente' AS ESTADO,
    'No aplica' AS DESCRIPCION,

    'Cliente facilities' AS ROL,

    0 AS PRODUCTO,

    'Facilities' AS NOMBRE_CANAL

FROM `sb-ecosistemaanalitico-lago.operaciones_log_fac.t_sedes_clientes_facilities`
where CODIGO_RECURSIVA  is not null or  CODIGO_RECURSIVA  != "" or CODIGO_RECURSIVA  != "001" or CODIGO_RECURSIVA  > "10"
QUALIFY ROW_NUMBER() OVER (
    PARTITION BY SPLIT(CODIGO_RECURSIVA, '-')[SAFE_OFFSET(0)]
    ORDER BY FECHA_CREACION DESC
) = 1 


)


--donantes
--roles ****



SELECT
    p.CODIGO_COMPANIA,
    p.TIPO_DOCUMENTO_TOMADOR,
    p.KEY_ID_TOMADOR,
    p.CODIGO_RAMO_EMISION,
    p.CODIGO_PRODUCTO,
    p.ESTADO,
    p.DESCRIPCION,
    p.ROL,
    p.PRODUCTO,
    p.NOMBRE_CANAL,

    d.AREA_RESTRICCION_DAV,
    d.DESCRIPCION_DAV,
    d.CLIENTE_PEP_DAV,
    d.CODIGO_RESTRICCION,
    d.DESCRIPCION_RESTRICCION,

    s.FECHA_TRANSACCION,
    s.USUARIO_TRANSACCION,
    s.FORMULARIO_TRANSACCION,
    s.RESULTADO_TRANSACCION,

    r.Nivel_de_Riesgo

FROM polizas_consolidadas p

LEFT JOIN (
    SELECT
        TIPO_IDENTIFICACION,
        KEY_ID,
        CODIGO_AREA_RESTRICCION AS AREA_RESTRICCION_DAV,
        DESCRIPCION_AREA_RESTRICCION AS DESCRIPCION_DAV,
        INDICADOR_CLIENTE_PEPS AS CLIENTE_PEP_DAV,
        CODIGO_RESTRICCION,
        DESCRIPCION_RESTRICCION
    FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_terceros_restringidos_con_davivienda`
) d
    ON p.TIPO_DOCUMENTO_TOMADOR = d.TIPO_IDENTIFICACION
   AND p.KEY_ID_TOMADOR = d.KEY_ID

LEFT JOIN (
    SELECT
        TIPO_DOCUMENTO_CLIENTE,
        KEY_ID_CLIENTE,
        FECHA_TRANSACCION,
        USUARIO_TRANSACCION,
        FORMULARIO_TRANSACCION,
        RESULTADO_TRANSACCION
    FROM (
        SELECT
            TIPO_DOCUMENTO_CLIENTE,
            KEY_ID_CLIENTE,
            FECHA_TRANSACCION,
            USUARIO_TRANSACCION,
            FORMULARIO_TRANSACCION,
            RESULTADO_TRANSACCION,
            ROW_NUMBER() OVER (
                PARTITION BY TIPO_DOCUMENTO_CLIENTE, KEY_ID_CLIENTE
                ORDER BY FECHA_TRANSACCION DESC
            ) AS rn
        FROM `sb-ecosistemaanalitico-lago.seguros_bolivar.t_registros_transacciones_formatos_sarlaft`
    )
    WHERE rn = 1
) s
    ON p.TIPO_DOCUMENTO_TOMADOR = s.TIPO_DOCUMENTO_CLIENTE
   AND p.KEY_ID_TOMADOR = s.KEY_ID_CLIENTE

LEFT JOIN (
    SELECT
        TIPO_DOCUMENTO,
        KEY_ID,
        Nivel_de_Riesgo
    FROM (
        SELECT
            TIPO_DOCUMENTO,
            KEY_ID,
            Nivel_de_Riesgo,
            ROW_NUMBER() OVER (
                PARTITION BY TIPO_DOCUMENTO, KEY_ID
                ORDER BY Nivel_de_Riesgo
            ) AS rn
        FROM `sb-ecosistemaanalitico-lago.cumplimiento_normativo_prod.score_riesgo_prod`
    )
    WHERE rn = 1
) r
    ON p.TIPO_DOCUMENTO_TOMADOR = r.TIPO_DOCUMENTO
   AND p.KEY_ID_TOMADOR = r.KEY_ID
```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
Tipo: BigQuery Query