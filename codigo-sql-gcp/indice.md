# Indice - Codigos SQL y GCP

## Descripcion General
Coleccion de scripts SQL y consultas BigQuery utilizados en el proyecto ARIS para analisis, reporteria y procesamiento de datos. Incluye consultas de datos maestros, transaccionalidad, validaciones y reportes ejecutivos.

Total de Archivos: 123 documentos .md
- GCP (BigQuery): 63 archivos
- SQL (Oracle Legacy): 60 archivos

Ultima Actualizacion: 2026-06-29

Nota: Todos los archivos han sido convertidos a formato .md con documentacion completa. El contenido SQL se encuentra preservado en su totalidad en cada documento.

---

## Estructura de Nombres

Todos los archivos siguen el patron:
[tipo]-[nombre-estandarizado]

Donde:
- tipo: gcp (BigQuery) o sql (Oracle)
- nombre-estandarizado: kebab-case normalizado del contenido

Ejemplo:
- gcp-activos-y-pasivos-personas-naturales.md
- sql-beneficiarios-de-polizas.md

---

## Categorias GCP (BigQuery)

### Datos Maestros
- gcp-actividad-economica-score.md
- gcp-beneficiario-final-core-terceros.md
- gcp-beneficiario-final-davivienda.md
- gcp-empleados.md
- gcp-juridicos.md
- gcp-naturales.md
- gcp-usuarios-idm-aris.md

### Clientes y Tomadores
- gcp-asegurados-vigentes.md
- gcp-cantidad-clientes-al-corte-de-un-mes.md
- gcp-cantidad-de-clientes---fecha-corte.md
- gcp-cantidad-de-clientes-vigentes-al-corte-actualizacos.md
- gcp-clientes-capitalizadora.md
- gcp-clientes-encriptados.md
- gcp-listas-cliente-vigente-davivienda.md
- gcp-tomadores-vigentes.md
- gcp-tomadores-asegurados-por-poliza-vigente.md

### Polizas
- gcp-comportamiento-de-polizas.md
- gcp-polizas-vigentes.md
- gcp-cancelacion-de-polizas.md
- gcp-valor-prima-y-valor-asegurado-polizas.md

### Transaccionalidad
- gcp-100-cuadras-transaccionalidad.md
- gcp-transaccionalidad-coupa.md
- gcp-transaccionalidad-polizas.md
- gcp-transaccionalidad-proveedores.md
- gcp-movimientos-capitalizadora.md
- gcp-movimiento-transaccional-capitalizadora.md

### Proveedores
- gcp-proveedores-ordenes-de-pago.md

### Riesgos y Seguridad
- gcp-codigo-revision-fraude.md
- gcp-terceros-restringidos.md
- gcp-codigos-terceros-restringidos-score.md
- gcp-pep-y-origen-de-recursos.md

### Score y Scoring
- gcp-canales-score.md
- gcp-jurisdicciones-score.md
- gcp-nacionalidad-score.md
- gcp-ocupacion-score.md
- gcp-productos-score.md
- gcp-sarlaft-score.md

### Productos
- gcp-arl-empresas-y-asegurados.md
- gcp-cantidad-de-polizas-empresas-publicas.md
- gcp-productos-vigentes-empresas-publicas.md
- gcp-titulos-de-capi-vigente-sin-bloqueados.md

### Otros
- gcp-agente-vendedor-por-poliza.md
- gcp-consulta-de-tomador-asegurado-beneficiario.md
- gcp-consulta-top-50-valores-asegurados.md
- gcp-el-libertador.md
- gcp-el-libertados.md
- gcp-facturacion-electronica.md
- gcp-formatos-de-conocmiento-traza.md
- gcp-incremento-valor-asegurado.md
- gcp-modelo-consulta-360.md
- gcp-nomina-activa.md
- gcp-ocupacion-agregada.md
- gcp-planta-y-medicos-de-cofnfianza-ips.md
- gcp-prima-anual.md
- gcp-prueba.md
- gcp-recaudos.md
- gcp-revisoria-fiscal-clientes-vigentes.md
- gcp-roles-de-terceros.md
- gcp-salario-nomina-empleados-bolivar.md
- gcp-siniestros.md
- gcp-sorteos-capi.md
- gcp-tabla-de-desencripcion.md
- gcp-idm.md

---

## Categorias SQL (Oracle Legacy)

### Datos Maestros
- sql-actividad-economica.md
- sql-nacionalidad-clientes.md
- sql-profesion-pn.md
- sql-codigo-de-localidades.md

### Terceros y Relaciones
- sql-base-de-terceros-restringidos.md
- sql-terceros-por-roles.md
- sql-juridicos-terceros.md
- sql-naturales-terceros.md
- sql-intermediario-proveedor.md
- sql-cliente-por-intermediario-o-outsorsing.md

### Beneficiarios
- sql-beneficiario-final.md
- sql-beneficiarios-de-polizas.md
- sql-beneficiarios-vpa.md

### Consultas de Clientes
- sql-consulta-tomador-y-asegurado.md
- sql-consulta-proveedores-con-ciiu-y-jurisdiccion.md

### Polizas
- sql-poliza-flotante.md
- sql-polizas-vigentes-tronador.md
- sql-polizas-anuladas-tronador.md
- sql-producto-923-y-922.md

### Siniestros
- sql-siniestros.md

### Transaccionalidad
- sql-transaccionaliad-polizas.md
- sql-transaccionalidad-clientes-compania-2-y-3-con-restriccion-en-listas-de-terceros.md
- sql-transaccionalidad-polizas-com-2-y-3.md
- sql-transaccionalidad-polizas-solo-terceros-restringidos.md
- sql-transaccionalidad-tesoreria-mod-segmentacion.md
- sql-movimiento-compania-2-y-3.md
- sql-movimientos-de-capitalizadora.md

### Proveedores y Tesoreria
- sql-ordenes-de-pago-tesoreria-proveedores.md
- sql-proveedores-internacionales.md
- sql-proveedores-ordenes-d-epago-de-proveedores.md
- sql-tesoreria-modelo-segementacion-capi.md
- sql-tesoreria-modelo-segementacion-seg-com.md

### Riesgos y Compliance
- sql-publicamente-expuesto-pep.md
- sql-cantidad-de-pep-por-ramo.md
- sql-cifra-restringidos-sobre-total-de-clientes.md
- sql-excetuados-sarlaft.md
- sql-cliente-con-formato-fatca.md

### Intercambio de Datos
- sql-intercambio-seguros.md
- sql-intercambio-davivienda.md
- sql-intercambio-davivineda.md

### Utilidades
- sql-telefonos-correos.md
- sql-datos-de-nat-y-jur-encriptar.md
- sql-divipol-jurisdicciones.md
- sql-log-de-modificaciones.md
- sql-biblioteca.md
- sql-explorar-variables.md
- sql-variables-financieras.md
- sql-traza-consulta-restringidos-intranet.md
- sql-scrap-url-datacredito.md
- sql-score-incremental-consulta-por-id.md
- sql-revision-de-recaudos-para-plantilla-de-efectivo.md
- sql-representante-legal.md
- sql-rf-jur-com1.md
- sql-rf-jur-com2.md
- sql-rf-jur-com3.md
- sql-rf-nat-com1.md
- sql-rf-nat-com2.md
- sql-rf-nat-com3.md
- sql-vpa-transacionalidad.md

---

## Como Usar

1. Abre el archivo correspondiente segun el tipo (gcp o sql)
2. Lee la descripcion o comenta del script
3. Copia el codigo SQL
4. Ejecuta en la base de datos correspondiente (BigQuery o Oracle)
5. Valida los resultados

---

Version: 1.0
Fecha: 2026-06-29
Total de Scripts: 123