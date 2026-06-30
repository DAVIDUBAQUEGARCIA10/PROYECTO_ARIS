# Índice Maestro - CALIDAD DE DATOS ARIS

## Descripción General
Colección completa de scripts SQL documentados para auditoría, validación y enriquecimiento de calidad de datos en el proyecto ARIS. Incluye:
- **CALIDAD DATOS GCP** (33 archivos): BigQuery - Auditoría de datos maestros
- **CALIDAD DE DATOS** (14 archivos): Oracle Legacy - Auditoría de completitud, validez y consistencia

**Total de Documentos**: 47  
**Bases de Datos**: BigQuery (`sb-ecosistemaanalitico-lago`) + Oracle (Legacy Seguros Bolívar)  
**Última Actualización**: 2026-06-29

---

## 📊 SECCIÓN 1: CALIDAD DATOS GCP (BigQuery)

### 1.1 Actividad Económica (CIIU)
Validación y normalización de códigos de actividad económica.

| Archivo | Descripción |
|---------|-------------|
| 01-calidad-actividad-economica-clientes-vigentes.md | Clientes vigentes con actividad |
| 02-calidad-actividad-economica-faltantes.md | Clientes sin CODIGO_CIIU ni ACTIVIDAD |
| 03-calidad-actividad-economica-faltantes-con-davivienda.md | **[PRIORITARIO]** Gaps remediables |
| 16-calidad-actividad-economica-revisoria-pj.md | Actividad desde revisoria fiscal PJ |
| 19-calidad-actividad-economica-casos-sin-davivienda.md | Gaps críticos (sin datos) |

### 1.2 Fecha de Constitución / Nacimiento Jurídico
Validación de fechas de constitución de PJ.

| Archivo | Descripción |
|---------|-------------|
| 04-calidad-fecha-constitucion-clientes-vigentes.md | Fecha de constitución clientes vigentes |
| 05-calidad-fecha-constitucion-faltantes.md | Clientes sin FECHA_NACIMIENTO |
| 06-calidad-fecha-constitucion-faltantes-con-davivienda.md | **[PRIORITARIO]** Gaps remediables |
| 20-calidad-fecha-constitucion-revisoria-pj.md | Fecha desde revisoria fiscal |

### 1.3 Beneficiario Final
Análisis de estructura accionaria.

| Archivo | Descripción |
|---------|-------------|
| 07-calidad-beneficiario-final-revisoria-fiscal.md | Beneficiarios identificados (COUNT) |
| 08-calidad-beneficiario-final-comparativa-revisoria.md | Beneficiarios faltantes |
| 09-calidad-beneficiario-final-relacionados-davivienda.md | Beneficiarios Davivienda vigentes |
| 17-calidad-beneficiario-final-gcp.md | Análisis participación accionaria |
| 24-calidad-status-beneficiario-final.md | Comparativa ARIS vs Davivienda |

### 1.4 Representante Legal
Validación de identificación del representante.

| Archivo | Descripción |
|---------|-------------|
| 10-calidad-representante-legal-clientes-vigentes.md | Representantes legales vigentes |
| 11-calidad-representante-legal-faltantes.md | Clientes sin representante legal |
| 12-calidad-representante-legal-faltantes-con-davivienda.md | **[PRIORITARIO]** Gaps remediables |
| 23-calidad-representante-legal-gcp.md | Representantes Davivienda |

### 1.5 Personas Naturales - Fecha de Nacimiento
Validación de fechas de nacimiento PN.

| Archivo | Descripción |
|---------|-------------|
| 14-calidad-fecha-nacimiento-persona-natural-base.md | Fechas PN (ARIS vs Davivienda) |
| 15-calidad-fecha-nacimiento-pn-faltante.md | PN sin fecha de nacimiento |
| 21-calidad-fecha-nacimiento-revisoria-pn.md | Fechas desde revisoria fiscal PN |

### 1.6 Datos Financieros
Auditoría de variables financieras.

| Archivo | Descripción |
|---------|-------------|
| 13-calidad-datos-financieros-activos-pasivos.md | Clientes con datos débiles o nulos |
| 25-calidad-variable-ingresos-empresas.md | Ingresos de empresas |
| 28-calidad-ingresos-egresos-general.md | Ingresos/egresos clientes vigentes |
| 31-calidad-variable-financiera-activo-pasivo.md | Activos y pasivos vigentes |
| 33-calidad-variables-financieras-completas.md | Vista completa variables financieras |

### 1.7 Davivienda - Enriquecimiento
Fuentes Davivienda para validación cruzada.

| Archivo | Descripción |
|---------|-------------|
| 22-calidad-ingresos-egresos-pn.md | Ingresos/egresos PN Davivienda |
| 26-calidad-activos-pasivos-pn.md | Activos y pasivos PN Davivienda |
| 27-calidad-davivienda-para-completar.md | Base Davivienda normalizada |
| 29-calidad-ingresos-egresos-davivienda.md | Ingresos/egresos PJ Davivienda |
| 32-calidad-variable-financiera-activo-pasivo-davivienda.md | Activos y pasivos PJ Davivienda |

### 1.8 Datos de Contacto
Auditoría de correo y teléfono.

| Archivo | Descripción |
|---------|-------------|
| 18-calidad-correo-personas-naturales.md | PN con correo faltante |
| 30-calidad-cantidad-registros-telefono-correo.md | Cobertura teléfono y correo |

---

## 🗄️ SECCIÓN 2: CALIDAD DE DATOS (Oracle Legacy)

### 2.1 Dimensiones de Calidad - DAMA Framework
| Archivo | Descripción |
|---------|-------------|
| 34-calidad-completitud.md | **Completitud**: Campos poblados vs faltantes |
| 35-calidad-validez.md | **Validez**: Datos cumplen reglas de negocio |
| 36-calidad-unicidad.md | **Unicidad**: No hay duplicados |
| 37-calidad-precision.md | **Precisión**: Datos exactos y consistentes |
| 38-calidad-consistencia.md | **Consistencia**: Coherencia entre tablas |

### 2.2 Auditoría por Dominio
| Archivo | Descripción |
|---------|-------------|
| 39-calidad-colaboradores-activos-rh.md | Talento Humano - Empleados vigentes |
| 40-calidad-cifras-registros-completos-faltantes.md | **Reporte Ejecutivo**: Conteo de registros |
| 41-calidad-formato-vs-variables.md | Análisis de estructura de datos |
| 42-calidad-cantidad-pep-cifras-cumplimiento.md | **Cumplimiento**: PEPS y listas restrictivas |
| 43-calidad-formato-conocimiento-vs-calidad.md | KYC vs Realidad en Sistemas |

### 2.3 Auditoría Integral
| Archivo | Descripción |
|---------|-------------|
| 44-calidad-informacion-producto-datos-sensibles.md | Privacidad de datos - Productos |
| 45-calidad-informacion-basica.md | Campos críticos: nombre, doc, estado |
| 46-calidad-nombre-terceros-completos.md | Terceros relacionados poblados |
| 47-calidad-data-completa-limpia.md | **Data Gold**: Registros limpios validados |

---

## 🎯 Guía de Uso por Rol

### Data Steward / Calidad de Datos
**Orden Recomendado:**
1. Ejecutar **40** (cifras generales)
2. Revisar **34-38** (dimensiones DAMA)
3. Profundizar en archivos **01-32** (BigQuery, por categoría)

### Data Engineer / Remedición
**Orden Recomendado:**
1. Ejecutar gaps: **03, 06, 12** (BigQuery - PRIORITARIO)
2. Cargar datos: **27, 29, 32** (Davivienda)
3. Validar: **01, 04, 10** (Auditoría post-carga)

### Auditor Interno / Cumplimiento
**Enfocarse en:**
- **42**: PEPS y listas restrictivas
- **09, 17, 24**: Estructura de propiedad
- **44**: Información de producto sensible
- **07, 08**: Identificación de beneficiarios

### Business Analyst
**Revisar:**
- **40**: Resumen ejecutivo
- **28, 31, 33**: Solidez financiera
- **01, 16, 19**: Actividad económica
- **04, 20**: Antigüedad empresarial

### Compliance Officer
**Críticos:**
- **42**: Cantidad PEP y cumplimiento
- **43**: KYC vs Realidad
- **47**: Data limpia para auditoría

---

## 🔄 Flujo de Remedición Recomendado

### Fase 1: Auditoría Inicial
```
40 (Cifras) → 34-38 (Dimensiones Oracle) → 01-06 (BigQuery Visión General)
```

### Fase 2: Identificar Gaps
```
02 (Act. Ec. faltantes) → 05 (Fecha faltantes) → 11 (Rep. Legal faltantes)
+ 15 (Fecha nac PN faltantes) + 18 (Correo PN faltantes)
```

### Fase 3: Gaps Remediables (PRIORITARIO)
```
03 (Act. Ec.) → 06 (Fecha const) → 12 (Rep. Legal)
↓
27, 29, 32 (Extraer Davivienda)
↓
Carga en ARIS
```

### Fase 4: Validación Post-Carga
```
01 (Auditoría Act. Ec.) → 04 (Fecha) → 10 (Rep. Legal)
+ 47 (Data limpia final)
```

---

## 📋 Clasificación por Característica

### Por Tipo de Problematica
**Completitud (Campos Faltantes)**
- 02, 05, 11, 15, 18 (BigQuery)
- 34 (Oracle)

**Validez (Datos Inválidos)**
- 13 (Datos financieros débiles)
- 35, 37 (Oracle)

**Consistencia (Discrepancias Fuentes)**
- 01, 04, 10, 14, 20, 21, 24, 28, 31, 33 (BigQuery)
- 38, 43 (Oracle)

**Unicidad (Duplicados)**
- 36 (Oracle)

### Por Facilidad de Remedición
**Fácil (Datos Davivienda Disponibles)**
- 03, 06, 12 (PRIORITARIO)

**Moderada (Requiere Validación)**
- 01, 04, 10, 24

**Compleja (Requiere Negocio)**
- 09, 17, 42 (Beneficiarios/PEPS)

---

## 💡 Métricas Clave de Calidad

Después de ejecutar estos scripts, monitorear:

| Métrica | Archivo | Objetivo |
|---------|---------|----------|
| % Completitud | 40 | > 95% |
| % Validez | 35, 37 | > 98% |
| % Unicidad | 36 | 100% |
| Consistencia | 38, 43 | 100% |
| Remediables | 03, 06, 12 | Reducir a 0 |
| Data Limpia | 47 | > 90% |

---

## 📞 Próximos Pasos

### Corto Plazo (1-2 semanas)
✅ Ejecutar 40, 34-38 (Línea base)  
✅ Identificar top 10 gaps por categoría  
✅ Estimar esfuerzo remedición  

### Mediano Plazo (1-2 meses)
→ Ejecutar fase de remedición (03, 06, 12)  
→ Cargar datos Davivienda  
→ Validar con auditoría post-carga  

### Largo Plazo (2-3 meses)
→ Automatizar auditorías mensuales  
→ Crear dashboard de calidad  
→ Establecer SLAs por métrica  

---

**Notas Técnicas**:
- Archivos GCP usan **BigQuery SQL**
- Archivos Oracle usan **PL/SQL/Oracle SQL**
- Todas las fechas validadas: rango 1901-01-01 a fecha actual
- Normalización: 9 dígitos estándar (sin DV)
- Enmascaramiento: datos sensibles según aplica

---

**Versión**: 1.0  
**Estado**: ✅ Completo - 47/47 archivos documentados  
**Autor**: Claude Code  
**Fecha**: 2026-06-29