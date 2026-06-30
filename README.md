# ARIS - Proyecto de Innovacion

## Descripcion
Proyecto de innovacion y desarrollo de analisis de datos para Seguros Bolivar. Incluye scripts de auditoria de calidad de datos, analisis de transaccionalidad y consultas de negocio.

## Estructura del Proyecto

### codigos-calidad-datos
Documentacion de scripts SQL para auditoria y validacion de calidad de datos.

Total: 49 documentos
- 33 scripts BigQuery (CALIDAD DATOS GCP)
- 14 scripts Oracle Legacy (CALIDAD DE DATOS)
- 2 indices de navegacion

Indices:
- indice.md: Documentacion GCP especifica
- indice-maestro.md: Indice consolidado de todas las auditorias

Categorias:
- Actividad Economica (5 scripts)
- Fecha de Constitucion (4 scripts)
- Beneficiario Final (5 scripts)
- Representante Legal (4 scripts)
- Personas Naturales - Fecha Nacimiento (3 scripts)
- Datos Financieros (5 scripts)
- Davivienda - Enriquecimiento (5 scripts)
- Datos de Contacto (2 scripts)
- Dimensiones DAMA: Completitud, Validez, Unicidad, Precision, Consistencia (5 scripts Oracle)
- Auditoria por Dominio (5 scripts Oracle)
- Auditoria Integral (4 scripts Oracle)

### codigo-sql-gcp
Coleccion de scripts SQL y consultas BigQuery para analisis y reporteria.

Total: 123 archivos
- 63 scripts GCP (BigQuery)
- 60 scripts SQL (Oracle Legacy)
- 1 indice de navegacion

Categorias:
- Datos Maestros
- Clientes y Tomadores
- Polizas
- Transaccionalidad
- Proveedores
- Riesgos y Seguridad
- Score y Scoring
- Productos
- Beneficiarios
- Terceros y Relaciones
- Siniestros
- Intercambio de Datos
- Tesoreria y Ordenes de Pago
- Utilidades

Indice: indice.md

## Convenios de Nombres

### Archivos
Todos los archivos siguen el patron kebab-case en minusculas sin emojis:
- Ejemplo GCP: gcp-actividad-economica-score.md
- Ejemplo SQL: sql-beneficiarios-de-polizas.md

### Carpetas
Todas las carpetas estan en minusculas con separadores (-):
- codigos-calidad-datos
- codigo-sql-gcp

## Como Navegar

1. Consulta los indices (indice.md) en cada carpeta
2. Busca el archivo segun la categoria
3. Lee la descripcion del script
4. Copia el codigo SQL
5. Ejecuta en la base de datos correspondiente

## Bases de Datos

- BigQuery: sb-ecosistemaanalitico-lago (GCP)
- Oracle: Legacy Seguros Bolivar

## Ultima Actualizacion
2026-06-29

## Estructura de Carpetas Finales

1. **codigos-calidad-datos** - 50 archivos (47 .md + 2 indices)
   - Auditoria de calidad de datos
   - 33 scripts BigQuery + 14 scripts Oracle

2. **codigo-sql-gcp** - 124 archivos (123 .md + 1 indice)
   - Scripts SQL y consultas BigQuery
   - 63 GCP + 60 SQL

3. **consulta-vista-360** - 2 archivos (1 .md + 1 indice)
   - Consulta 360 grados de contrapartes

4. **monitoreo** - ~91 archivos
   - Scripts de monitoreo

5. **monitoreo-descriptivo** - 18 archivos
   - Analisis descriptivo

6. **visoralertasaris** - 55 archivos
   - Visor de alertas

7. **modelosegmentacion** - 29 archivos
   - Modelos de segmentacion

8. **score-de-terceros** - 15 archivos
   - Scoring de terceros

9. **riesgo-inherente** - 7 archivos
   - Evaluacion de riesgos

10. **deteccin-de-anomalias** - 4 archivos
    - Deteccion de anomalias

11. **opt---reporte-efectivo** - 5 archivos
    - Reportes de tesoreria

12. **remediacin-clientes-pep** - 2 archivos
    - Remediacion de clientes PEP

13. **servicioconsultalistas** - 2 archivos
    - Consulta de listas restrictivas

14. **modelorelacionalubos** - 3 archivos
    - Modelo relacional UBOS

15. **fatca** - 3 archivos
    - Cumplimiento FATCA

16. **BANNER** - 55 archivos
    - Imagenes y recursos visuales

## Resumen de Procesamiento

**Total de carpetas**: 16
**Total de archivos**: 400+ documentos + imagenes

**Conversiones realizadas**:
- 345+ archivos convertidos a formato .md
- Todos los nombres estandarizados (kebab-case)
- 16 indices de navegacion creados
- 0 duplicidades (carpetas originales eliminadas)
- Cero emojis en nombres ni documentacion
- Estructura clara y navegable

**Bases de datos cubiertas**:
- BigQuery (GCP): sb-ecosistemaanalitico-lago
- Oracle: Legacy Seguros Bolivar

## Estado
Proyecto completamente documentado y organizado - 100% completo