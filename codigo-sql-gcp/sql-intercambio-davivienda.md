# Intercambio Davivienda

## Descripcion
Script de sql para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: Oracle
Categoria: Varios

## Codigo SQL

```sql
SELECT 
  '3' AS Tipo_de_Identificacion,
  CONCAT(numero_documento, digito_chequeo) AS Numero_de_identificacion,
  'Persona Juridica' AS Tipo_de_cliente
FROM juridicos
ORDER BY CONCAT(numero_documento, digito_chequeo) DESC;

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
