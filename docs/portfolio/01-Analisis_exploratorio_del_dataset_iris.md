---
title: "01—Análisis exploratorio del dataset Iris"
date: 2025-08-21
---

# Análisis exploratorio del dataset Iris

## Contexto
En la primera práctica del curso trabajé con el dataset clásico de **flores Iris**. El objetivo fue conocer el flujo de trabajo en un notebook, explorar las variables del dataset y generar las primeras visualizaciones con Python para identificar patrones iniciales.

## Objetivos
- Configurar el entorno de trabajo en Jupyter Notebook.  
- Importar librerías clave para análisis de datos (**pandas, matplotlib, seaborn**).  
- Explorar y describir las variables del dataset Iris.  
- Generar visualizaciones básicas para observar relaciones entre variables.  
- Formular preguntas de negocio que guíen futuros análisis.

## Actividades (con tiempos estimados)
- Configuración de entorno y librerías — 30 min  
- Carga y exploración inicial del dataset — 45 min  
- Estadísticas descriptivas y preguntas de negocio — 45 min  
- Visualización de correlaciones y pairplots — 60 min  
- Documentación en el portafolio — 30 min  

## Desarrollo
1. **Preparación del entorno:** instalé y verifiqué versiones de librerías (pandas, seaborn, matplotlib).  
2. **Carga del dataset:** usé `sns.load_dataset('iris')` para trabajar directamente con el conjunto de datos.  
3. **Exploración inicial:** obtuve estadísticas descriptivas con `df.describe()`, verifiqué tipos de variables y balance de clases (50 observaciones por especie).  
4. **Preguntas de negocio planteadas:**  
   - ¿Cuál es la longitud promedio de pétalos y sépalos por especie?  
   - ¿Existe correlación clara entre ancho y largo de los pétalos?  
   - ¿Se pueden separar las especies usando solo dos variables?  
   - ¿Qué variable presenta mayor variabilidad en cada especie?  
   - ¿La distribución de observaciones está equilibrada entre especies?  
5. **Visualizaciones:**  
   - **Mapa de calor de correlaciones** para identificar relaciones fuertes (ej: alta correlación entre petal_length y petal_width).  
   - **Pairplot** de seaborn mostrando cómo se distribuyen las tres especies en función de las variables morfológicas.

## Evidencias
- [Notebook completo](../recursos_files/uno.ipynb)

## Reflexión
Este primer trabajo me permitió familiarizarme con el entorno de análisis y con la lógica de los notebooks.  
Aprendí a organizar un flujo de trabajo básico: **cargar datos → explorarlos → plantear preguntas → visualizar resultados**.  
Lo más valioso fue comprobar cómo las visualizaciones ayudan a identificar patrones que no son evidentes en tablas numéricas.  
Un próximo paso será aplicar técnicas de modelado predictivo sobre este dataset y otros más complejos, para pasar de la exploración a la predicción.
