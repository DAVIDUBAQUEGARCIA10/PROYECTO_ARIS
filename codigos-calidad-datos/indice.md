# Indice de Documentacion - CALIDAD DE DATOS GCP

## Descripcion General
Coleccion de scripts SQL documentados para auditoria, validacion y enriquecimiento de calidad de datos en el proyecto ARIS. Todos los archivos estan organizados por categoria y nombrados con estandar kebab-case.

Total de Documentos: 33  
Base de Datos Principal: BigQuery (sb-ecosistemaanalitico-lago)  
Ultima Actualizacion: 2026-06-29

---

## Tabla de Contenidos

### 1. Actividad Economica (CIIU)
Validacion y normalizacion de codigos de actividad economica entre ARIS y Davivienda.

| # | Archivo | Descripcion |
|---|---------|-------------|
| 1 | 01-calidad-actividad-economica-clientes-vigentes.md | Actividad economica de clientes con productos vigentes |
| 2 | 02-calidad-actividad-economica-faltantes.md | Clientes sin CODIGO_CIIU ni ACTIVIDAD_ECONOMICA |
| 3 | 03-calidad-actividad-economica-faltantes-con-davivienda.md | Faltantes remediables con datos Davivienda (PRIORITARIO) |
| 16 | 16-calidad-actividad-economica-revisoria-pj.md | Actividad economica desde revisoria fiscal PJ |
| 19 | 19-calidad-actividad-economica-casos-sin-davivienda.md | Gaps criticos (sin datos en ARIS ni Davivienda) |

### 2. Fecha de Constitucion / Nacimiento Juridico
Validacion de fechas de constitucion de personas juridicas.

| # | Archivo | Descripcion |
|---|---------|-------------|
| 4 | 04-calidad-fecha-constitucion-clientes-vigentes.md | Fecha de constitucion de clientes vigentes |
| 5 | 05-calidad-fecha-constitucion-faltantes.md | Clientes sin FECHA_NACIMIENTO |
| 6 | 06-calidad-fecha-constitucion-faltantes-con-davivienda.md | Faltantes remediables con Davivienda (PRIORITARIO) |
| 20 | 20-calidad-fecha-constitucion-revisoria-pj.md | Fecha de constitucion desde revisoria fiscal |

### 3. Beneficiario Final
Analisis de estructura accionaria y beneficiarios finales.

| # | Archivo | Descripcion |
|---|---------|-------------|
| 7 | 07-calidad-beneficiario-final-revisoria-fiscal.md | Beneficiarios identificados en revisoria (COUNT) |
| 8 | 08-calidad-beneficiario-final-comparativa-revisoria.md | Beneficiarios faltantes en revisoria |
| 9 | 09-calidad-beneficiario-final-relacionados-davivienda.md | Beneficiarios segun Davivienda (socios/accionistas vigentes) |
| 17 | 17-calidad-beneficiario-final-gcp.md | Beneficiarios con analisis de participacion accionaria |
| 24 | 24-calidad-status-beneficiario-final.md | Comparativa de composicion accionaria ARIS vs Davivienda |

### 4. Representante Legal
Validacion de identificacion de representante legal.

| # | Archivo | Descripcion |
|---|---------|-------------|
| 10 | 10-calidad-representante-legal-clientes-vigentes.md | Representantes legales de clientes vigentes |
| 11 | 11-calidad-representante-legal-faltantes.md | Clientes sin representante legal registrado |
| 12 | 12-calidad-representante-legal-faltantes-con-davivienda.md | Faltantes remediables con Davivienda (PRIORITARIO) |
| 23 | 23-calidad-representante-legal-gcp.md | Representantes legales desde Davivienda |

### 5. Personas Naturales - Fecha de Nacimiento
Validacion de fechas de nacimiento de personas naturales.

| # | Archivo | Descripcion |
|---|---------|-------------|
| 14 | 14-calidad-fecha-nacimiento-persona-natural-base.md | Fechas de nacimiento PN base (ARIS vs Davivienda) |
| 15 | 15-calidad-fecha-nacimiento-pn-faltante.md | PN sin fecha de nacimiento |
| 21 | 21-calidad-fecha-nacimiento-revisoria-pn.md | Fechas de nacimiento desde revisoria fiscal PN |

### 6. Datos Financieros
Auditoria de integridad de variables financieras.

| # | Archivo | Descripcion |
|---|---------|-------------|
| 13 | 13-calidad-datos-financieros-activos-pasivos.md | Clientes con datos financieros muy debiles o nulos |
| 25 | 25-calidad-variable-ingresos-empresas.md | Variable de ingresos de empresas |
| 28 | 28-calidad-ingresos-egresos-general.md | Ingresos y egresos generales (clientes vigentes) |
| 31 | 31-calidad-variable-financiera-activo-pasivo.md | Activos y pasivos de clientes vigentes |
| 33 | 33-calidad-variables-financieras-completas.md | Vista completa de variables financieras |

### 7. Davivienda - Enriquecimiento
Fuentes de datos de Davivienda para enriquecimiento y validacion cruzada.

| # | Archivo | Descripcion |
|---|---------|-------------|
| 22 | 22-calidad-ingresos-egresos-pn.md | Ingresos/egresos PN desde Davivienda |
| 26 | 26-calidad-activos-pasivos-pn.md | Activos y pasivos PN desde Davivienda |
| 27 | 27-calidad-davivienda-para-completar.md | Base Davivienda normalizada para enriquecimiento |
| 29 | 29-calidad-ingresos-egresos-davivienda.md | Ingresos/egresos PJ desde Davivienda |
| 32 | 32-calidad-variable-financiera-activo-pasivo-davivienda.md | Activos y pasivos PJ desde Davivienda |

### 8. Datos de Contacto
Auditoria de integridad de datos de contacto (correo, telefono).

| # | Archivo | Descripcion |
|---|---------|-------------|
| 18 | 18-calidad-correo-personas-naturales.md | PN con correo faltante |
| 30 | 30-calidad-cantidad-registros-telefono-correo.md | Cobertura de telefono y correo por cliente |

---

## Clasificacion por Tipo

### Auditoria de Campos Faltantes
Identifican que informacion esta incompleta:
- 02, 05, 11, 15, 18 (BigQuery)

### Gaps Remediables (PRIORITARIO)
Datos faltantes pero disponibles en Davivienda:
- 03 Actividad Economica
- 06 Fecha de Constitucion
- 12 Representante Legal

### Auditoria Cruzada (ARIS vs Davivienda)
Validacion de consistencia entre fuentes:
- 01, 04, 10, 14, 20, 21, 24, 28, 31, 33

### Enriquecimiento de Datos
Fuentes externas para completar informacion:
- 09, 16, 17, 19, 22, 23, 26, 27, 29, 32

### Auditoria General
Analisis sin filtros especificos:
- 07, 08, 13, 18, 25, 30

---

## Casos de Uso por Rol

### Data Steward / Calidad de Datos
Comienza con:
1. Revisar archivos 02, 05, 11, 15 (que falta)
2. Priorizar 03, 06, 12 (que es facil de arreglar)
3. Auditar 01, 04, 10, 20 (validacion general)

### Data Engineer / Remediacion
Ejecutar en orden:
1. 03, 06, 12: Cargar datos desde Davivienda
2. 27, 29, 32: Normalizar y validar
3. 28, 31, 33: Verificar despues de carga

### Auditor Interno / Cumplimiento
Enfocarse en:
- 09, 17, 24: Estructura de propiedad
- 07, 08: Identificacion de beneficiarios
- 30: Trazabilidad de contacto

### Business Analyst
Revisar:
- 13, 25, 28, 31, 33: Solidez financiera
- 01, 16, 19: Actividad economica
- 04, 20: Antiguedad empresarial

---

## Como Usar Esta Documentacion

### Para Ejecutar un Script
1. Abre el archivo .md correspondiente
2. Copia el bloque SQL
3. Ejecuta en BigQuery
4. Revisa los resultados contra la descripcion esperada

### Para Entender Que Auditar
1. Consulta la tabla Clasificacion por Tipo
2. Selecciona el grupo que corresponde
3. Ejecuta los scripts en orden de numeracion

### Para Remediar Datos
1. Ejecuta primero los archivos de gaps remediables (03, 06, 12)
2. Usa 27, 29, 32 para obtener datos de Davivienda
3. Carga en ARIS segun tu proceso ETL
4. Verifica con archivos de auditoria cruzada (01, 04, 10, 14, 20, 21)

---

## Nomenclatura de Archivos

Todos los archivos siguen el patron:
NN-calidad-CATEGORIA-SUBCATEGORIA-VARIANTE.md

Donde:
- NN: Numero secuencial (01-33)
- calidad: Prefijo estandar
- CATEGORIA: Tipo de dato (actividad-economica, fecha-constitucion, etc.)
- SUBCATEGORIA: Scope (clientes-vigentes, faltantes, davivienda, etc.)
- VARIANTE: Diferenciador adicional (con-davivienda, revisoria, etc.)

Ejemplo:
- 06-calidad-fecha-constitucion-faltantes-con-davivienda.md
  - Categoria: Fecha de Constitucion
  - Subcategoria: Faltantes
  - Variante: Con datos en Davivienda

---

## Proximos Pasos

### Corto Plazo
1. Documentar todos los scripts (COMPLETADO)
2. Ejecutar auditoria inicial con archivos 01-06, 10, 13
3. Identificar top 10 gaps de mayor impacto

### Mediano Plazo
1. Crear planes de remediacion para archivos PRIORITARIO (03, 06, 12)
2. Desarrollar automaciones para refresh mensual
3. Integrar en dashboard de calidad de datos

### Largo Plazo
1. Establecer SLAs para cada metrica
2. Crear alertas automaticas para degradacion
3. Expandir a otras categorias de datos

---

Notas Importantes:
- Todos los scripts usan BigQuery (GCP)
- Las fechas estan validadas: rango 1901-01-01 a fecha actual
- Normalizacion a 9 digitos es estandar (sin digito verificador)
- Enmascaramiento de datos sensibles donde aplica (correos, etc.)
- Las tablas usan formato de BigQuery (proyecto.dataset.tabla)

Version: 1.0  
Ultima Actualizacion: 2026-06-29  
Estado: Completo - 33/33 archivos documentados