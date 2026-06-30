# Clientes Capitalizadora

## Descripcion
Script de gcp para extraccion y analisis de datos en el proyecto ARIS.

## Tipo
Base de datos: BigQuery
Categoria: Clientes

## Codigo SQL

```sql
-- Consulta para obtener registros correspondientes a la fecha de corte más reciente
SELECT 
  fecha_corte,
  tipo_documento,
  key_id,
  titulo,
  tiene_saldo,
  titulos_al_dia
FROM 
  `sb-ecosistemaanalitico-lago.capitalizadora.t_clientes_capitalizadora`
WHERE 
  fecha_corte = (
    -- Subconsulta que obtiene la fecha de corte más reciente en la tabla
    SELECT MAX(fecha_corte)
    FROM `sb-ecosistemaanalitico-lago.capitalizadora.t_clientes_capitalizadora`
  );

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
