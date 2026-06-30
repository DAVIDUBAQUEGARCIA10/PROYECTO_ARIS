# Indice Maestro - CALIDAD DE DATOS ARIS

## Descripcion General
Coleccion completa de scripts SQL documentados para auditoria, validacion y enriquecimiento de calidad de datos en el proyecto ARIS. Incluye:
- CALIDAD DATOS GCP (33 archivos): BigQuery - Auditoria de datos maestros
- CALIDAD DE DATOS (14 archivos): Oracle Legacy - Auditoria de completitud, validez y consistencia

Total de Documentos: 47  
Bases de Datos: BigQuery (sb-ecosistemaanalitico-lago) + Oracle (Legacy Seguros Bolivar)  
Ultima Actualizacion: 2026-06-29

---

## SECCION 1: CALIDAD DATOS GCP (BigQuery)

### 1.1 Actividad Economica (CIIU)
Validacion y normalizacion de codigos de actividad economica.

| Archivo | Descripcion |
|---------|-------------|
| 01-calidad-actividad-economica-clientes-vigentes.md | Clientes vigentes con actividad |
| 02-calidad-actividad-economica-faltantes.md | Clientes sin CODIGO_CIIU ni ACTIVIDAD |
| 03-calidad-actividad-economica-faltantes-con-davivienda.md | [PRIORITARIO] Gaps remediables |
| 16-calidad-actividad-economica-revisoria-pj.md | Actividad desde revisoria fiscal PJ |
| 19-calidad-actividad-economica-casos-sin-davivienda.md | Gaps criticos (sin datos) |

### 1.2 Fecha de Constitucion / Nacimiento Juridico
Validacion de fechas de constitucion de PJ.

| Archivo | Descripcion |
|---------|-------------|
| 04-calidad-fecha-constitucion-clientes-vigentes.md | Fecha de constitucion clientes vigentes |
| 05-calidad-fecha-constitucion-faltantes.md | Clientes sin FECHA_NACIMIENTO |
| 06-calidad-fecha-constitucion-faltantes-con-davivienda.md | [PRIORITARIO] Gaps remediables |
| 20-calidad-fecha-constitucion-revisoria-pj.md | Fecha desde revisoria fiscal |

### 1.3 Beneficiario Final
Analisis de estructura accionaria.

| Archivo | Descripcion |
|---------|-------------|
| 07-calidad-beneficiario-final-revisoria-fiscal.md | Beneficiarios identificados (COUNT) |
| 08-calidad-beneficiario-final-comparativa-revisoria.md | Beneficiarios faltantes |
| 09-calidad-beneficiario-final-relacionados-davivienda.md | Beneficiarios Davivienda vigentes |
| 17-calidad-beneficiario-final-gcp.md | Analisis participacion accionaria |
| 24-calidad-status-beneficiario-final.md | Comparativa ARIS vs Davivienda |

### 1.4 Representante Legal
Validacion de identificacion del representante.

| Archivo | Descripcion |
|---------|-------------|
| 10-calidad-representante-legal-clientes-vigentes.md | Representantes legales vigentes |
| 11-calidad-representante-legal-faltantes.md | Clientes sin representante legal |
| 12-calidad-representante-legal-faltantes-con-davivienda.md | [PRIORITARIO] Gaps remediables |
| 23-calidad-representante-legal-gcp.md | Representantes Davivienda |

### 1.5 Personas Naturales - Fecha de Nacimiento
Validacion de fechas de nacimiento PN.

| Archivo | Descripcion |
|---------|-------------|
| 14-calidad-fecha-nacimiento-persona-natural-base.md | Fechas PN (ARIS vs Davivienda) |
| 15-calidad-fecha-nacimiento-pn-faltante.md | PN sin fecha de nacimiento |
| 21-calidad-fecha-nacimiento-revisoria-pn.md | Fechas desde revisoria fiscal PN |

### 1.6 Datos Financieros
Auditoria de variables financieras.

| Archivo | Descripcion |
|---------|-------------|
| 13-calidad-datos-financieros-activos-pasivos.md | Clientes con datos debiles o nulos |
| 25-calidad-variable-ingresos-empresas.md | Ingresos de empresas |
| 28-calidad-ingresos-egresos-general.md | Ingresos/egresos clientes vigentes |
| 31-calidad-variable-financiera-activo-pasivo.md | Activos y pasivos vigentes |
| 33-calidad-variables-financieras-completas.md | Vista completa variables financieras |

### 1.7 Davivienda - Enriquecimiento
Fuentes Davivienda para validacion cruzada.

| Archivo | Descripcion |
|---------|-------------|
| 22-calidad-ingresos-egresos-pn.md | Ingresos/egresos PN Davivienda |
| 26-calidad-activos-pasivos-pn.md | Activos y pasivos PN Davivienda |
| 27-calidad-davivienda-para-completar.md | Base Davivienda normalizada |
| 29-calidad-ingresos-egresos-davivienda.md | Ingresos/egresos PJ Davivienda |
| 32-calidad-variable-financiera-activo-pasivo-davivienda.md | Activos y pasivos PJ Davivienda |

### 1.8 Datos de Contacto
Auditoria de correo y telefono.

| Archivo | Descripcion |
|---------|-------------|
| 18-calidad-correo-personas-naturales.md | PN con correo faltante |
| 30-calidad-cantidad-registros-telefono-correo.md | Cobertura telefono y correo |

---

## SECCION 2: CALIDAD DE DATOS (Oracle Legacy)

### 2.1 Dimensiones de Calidad - DAMA Framework
| Archivo | Descripcion |
|---------|-------------|
| 34-calidad-completitud.md | Completitud: Campos poblados vs faltantes |
| 35-calidad-validez.md | Validez: Datos cumplen reglas de negocio |
| 36-calidad-unicidad.md | Unicidad: No hay duplicados |
| 37-calidad-precision.md | Precision: Datos exactos y consistentes |
| 38-calidad-consistencia.md | Consistencia: Coherencia entre tablas |

### 2.2 Auditoria por Dominio
| Archivo | Descripcion |
|---------|-------------|
| 39-calidad-colaboradores-activos-rh.md | Talento Humano - Empleados vigentes |
| 40-calidad-cifras-registros-completos-faltantes.md | Reporte Ejecutivo: Conteo de registros |
| 41-calidad-formato-vs-variables.md | Analisis de estructura de datos |
| 42-calidad-cantidad-pep-cifras-cumplimiento.md | Cumplimiento: PEPS y listas restrictivas |
| 43-calidad-formato-conocimiento-vs-calidad.md | KYC vs Realidad en Sistemas |

### 2.3 Auditoria Integral
| Archivo | Descripcion |
|---------|-------------|
| 44-calidad-informacion-producto-datos-sensibles.md | Privacidad de datos - Productos |
| 45-calidad-informacion-basica.md | Campos criticos: nombre, doc, estado |
| 46-calidad-nombre-terceros-completos.md | Terceros relacionados poblados |
| 47-calidad-data-completa-limpia.md | Data Gold: Registros limpios validados |

---

## Guia de Uso por Rol

### Data Steward / Calidad de Datos
Orden Recomendado:
1. Ejecutar 40 (cifras generales)
2. Revisar 34-38 (dimensiones DAMA)
3. Profundizar en archivos 01-32 (BigQuery, por categoria)

### Data Engineer / Remediacion
Orden Recomendado:
1. Ejecutar gaps: 03, 06, 12 (BigQuery - PRIORITARIO)
2. Cargar datos: 27, 29, 32 (Davivienda)
3. Validar: 01, 04, 10 (Auditoria post-carga)

### Auditor Interno / Cumplimiento
Enfocarse en:
- 42: PEPS y listas restrictivas
- 09, 17, 24: Estructura de propiedad
- 44: Informacion de producto sensible
- 07, 08: Identificacion de beneficiarios

### Business Analyst
Revisar:
- 40: Resumen ejecutivo
- 28, 31, 33: Solidez financiera
- 01, 16, 19: Actividad economica
- 04, 20: Antiguedad empresarial

### Compliance Officer
Criticos:
- 42: Cantidad PEP y cumplimiento
- 43: KYC vs Realidad
- 47: Data limpia para auditoria

---

## Flujo de Remediacion Recomendado

### Fase 1: Auditoria Inicial
40 (Cifras) -> 34-38 (Dimensiones Oracle) -> 01-06 (BigQuery Vision General)

### Fase 2: Identificar Gaps
02 (Act. Ec. faltantes) -> 05 (Fecha faltantes) -> 11 (Rep. Legal faltantes)
+ 15 (Fecha nac PN faltantes) + 18 (Correo PN faltantes)

### Fase 3: Gaps Remediables (PRIORITARIO)
03 (Act. Ec.) -> 06 (Fecha const) -> 12 (Rep. Legal)
|
27, 29, 32 (Extraer Davivienda)
|
Carga en ARIS

### Fase 4: Validacion Post-Carga
01 (Auditoria Act. Ec.) -> 04 (Fecha) -> 10 (Rep. Legal)
+ 47 (Data limpia final)

---

## Clasificacion por Caracteristica

### Por Tipo de Problematica
Completitud (Campos Faltantes):
- 02, 05, 11, 15, 18 (BigQuery)
- 34 (Oracle)

Validez (Datos Invalidos):
- 13 (Datos financieros debiles)
- 35, 37 (Oracle)

Consistencia (Discrepancias Fuentes):
- 01, 04, 10, 14, 20, 21, 24, 28, 31, 33 (BigQuery)
- 38, 43 (Oracle)

Unicidad (Duplicados):
- 36 (Oracle)

### Por Facilidad de Remediacion
Facil (Datos Davivienda Disponibles):
- 03, 06, 12 (PRIORITARIO)

Moderada (Requiere Validacion):
- 01, 04, 10, 24

Compleja (Requiere Negocio):
- 09, 17, 42 (Beneficiarios/PEPS)

---

## Metricas Clave de Calidad

Despues de ejecutar estos scripts, monitorear:

| Metrica | Archivo | Objetivo |
|---------|---------|----------|
| % Completitud | 40 | > 95% |
| % Validez | 35, 37 | > 98% |
| % Unicidad | 36 | 100% |
| Consistencia | 38, 43 | 100% |
| Remediables | 03, 06, 12 | Reducir a 0 |
| Data Limpia | 47 | > 90% |

---

## Proximos Pasos

### Corto Plazo (1-2 semanas)
Ejecutar 40, 34-38 (Linea base)
Identificar top 10 gaps por categoria
Estimar esfuerzo remediacion

### Mediano Plazo (1-2 meses)
Ejecutar fase de remediacion (03, 06, 12)
Cargar datos Davivienda
Validar con auditoria post-carga

### Largo Plazo (2-3 meses)
Automatizar auditorias mensuales
Crear dashboard de calidad
Establecer SLAs por metrica

---

Notas Tecnicas:
- Archivos GCP usan BigQuery SQL
- Archivos Oracle usan PL/SQL/Oracle SQL
- Todas las fechas validadas: rango 1901-01-01 a fecha actual
- Normalizacion: 9 digitos estandar (sin DV)
- Enmascaramiento: datos sensibles segun aplica

Version: 1.0  
Estado: Completo - 47/47 archivos documentados  
Autor: Claude Code  
Fecha: 2026-06-29