# Representante Legal

## Descripcion
Script de sql para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: Oracle
Categoria: Varios

## Codigo SQL

```sql
SELECT NUMERO_DOCUMENTO, NUMERO_DOCUMENTO_REPRESENTANTE, TIPDOC_CODIGO_IDENTIFICADO_POR, REPRESENTANTE_LEGAL 
FROM JURIDICOS
WHERE NUMERO_DOCUMENTO_REPRESENTANTE IS NOT NULL

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
