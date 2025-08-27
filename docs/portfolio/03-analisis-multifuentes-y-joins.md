---
title: "03 — Analisis NYC Taxis (EDA multifuentes y Joins)"
date: 2025-08-27
---

# Analisis NYC Taxis (EDA multifuentes y Joins)

## Contexto
En esta práctica trabajamos con datos de taxis de Nueva York y fuentes adicionales, con el objetivo de realizar un análisis exploratorio integrando múltiples datasets. La tarea principal fue practicar operaciones de **join** para combinar información complementaria y generar nuevos insights.

La consigna completa se encuentra en: [EDA multifuentes y joins — UT1](https://juanfkurucz.com/ucu-id/ut1/04-eda-multifuentes-joins/).

## Objetivos
- Aplicar operaciones de **merge/join** entre diferentes fuentes de datos.  
- Practicar un análisis exploratorio de datos (EDA) sobre un dataset real y complejo.  
- Identificar problemas de calidad de datos y resolverlos para poder unir fuentes.  
- Generar conclusiones a partir de la integración multifuente.  

## Actividades (con tiempos estimados)
- Revisión de la consigna y datasets — 20 min  
- Carga y exploración inicial de los datos — 30 min  
- Limpieza y preparación para joins — 30 min  
- Realización de joins y creación de nuevas tablas integradas — 40 min  
- Análisis de resultados — 30 min  
- Documentación en el portafolio — 30 min  

## Desarrollo
1. **Carga de datos**: se trabajó con el dataset de viajes de taxis de NYC y fuentes auxiliares, verificando la estructura y el contenido de cada tabla.  
2. **Exploración inicial**: se revisaron las dimensiones, los tipos de datos y la existencia de valores faltantes.  
3. **Limpieza de datos**: se aplicaron transformaciones para asegurar la consistencia de las columnas clave utilizadas en los joins.  
4. **Operaciones de join**: se realizaron merges entre tablas para enriquecer la información, como la relación entre viajes, ubicaciones y tarifas.  
5. **Resultados**: se obtuvieron tablas consolidadas que permiten tener una visión más completa de los viajes, con datos combinados de distintas fuentes.  

## Evidencias
- [Notebook completo en nbviewer](https://nbviewer.org/github/naguer019/IA-portfolio/blob/main/docs/recursos_files/Practica_4_nyc_taxis.ipynb)


## Reflexión
Esta práctica me permitió reforzar la importancia de trabajar con múltiples fuentes de datos y cómo los **joins** son fundamentales para enriquecer un análisis.  
Lo más desafiante fue asegurar que las claves de unión coincidieran y manejar los datos faltantes.  
Lo más valioso fue comprobar cómo la integración de datasets permite descubrir patrones y relaciones que no serían visibles analizando cada fuente por separado.  
Un próximo paso sería profundizar en métricas descriptivas y aplicar visualizaciones en prácticas futuras para interpretar mejor la información integrada.
Aunque no pudimos realizar la parte práctica del bonus, considero que fue interesante ya entrar en prefect y las herramientas que nos ofrece.

## Referencias
- Consigna: [EDA multifuentes y joins — UT1](https://juanfkurucz.com/ucu-id/ut1/04-eda-multifuentes-joins/)  
- Dataset: NYC Taxis  
- Documentación de pandas (merge, join, concat)
