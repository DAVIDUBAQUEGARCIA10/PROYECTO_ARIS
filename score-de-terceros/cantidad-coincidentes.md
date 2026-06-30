# Cantidad Coincidentes

## Descripcion
Script o consulta del proyecto ARIS.

## Tipo
Archivo: 
Carpeta: Score de Terceros

## Contenido

```
SELECT CODIGO_COMPANIA,Coincidencia, COUNT(DISTINCT  KEY_ID)
    FROM `sb-sandbox-usuarios.sandbox_cumplimiento.Score_de_terceros_Tabla_C` 
    --WHERE Coincidencia IN('ARIS Bajo','ARIS Arriba',"Coincide","Vacio Plus","Vacio ARIS")
    GROUP BY CODIGO_COMPANIA,Coincidencia

```

---

Fecha: 2026-06-29
Proyecto: ARIS - Seguros Bolivar
Archivo Original: Cantidad Coincidentes
