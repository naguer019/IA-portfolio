---
title: "02-Análisis del catálogo de Netflix"
date: 2025-08-21
---

# Análisis del catálogo de Netflix

## Contexto
Trabajo de análisis de datos para explorar la evolución del catálogo de Netflix y entender tendencias por tipo de contenido, mercados y géneros.

## Objetivos
- Analizar la evolución temporal de títulos (Movies vs TV Shows).
- Identificar concentración por países/mercados.
- Detectar géneros predominantes y oportunidades.

## Actividades (con tiempos estimados)
- Recolección y limpieza — 40 min  
- Visualización y análisis — 70 min  
- Conclusiones — 30 min

## Desarrollo
Se observó un crecimiento marcado de las series desde 2017, superando a las películas. Esto sugiere un viraje estratégico hacia producciones seriadas originales.  
En mercados, EE. UU. e India concentran gran parte del catálogo; conviene diversificar con producciones locales en regiones emergentes.  
En géneros, destacan dramas, comedias y películas internacionales; se recomienda reforzar géneros transversales y explorar nichos (anime, documentales, realities).

## Evidencias
- [Notebook completo en nbviewer](https://nbviewer.org/github/naguer019/IA-portfolio/blob/main/docs/recursos_files/analysis%20parte%203%20netflix%20ing%20datos.ipynb)

### Análisis de cada visualización
Detección y visualización de datos faltantes:
![Detección y visualización de datos faltantes](../assets/datos_faltantes_ent2.png){ width="680" }

Detección de outliers y anomalías:
![Detección de outliers y anomalías](../assets/deteccion_outliers_anomalias_ent2.png){ width="680" }

Analisis de tipos de contenido:
![Analisis de tipos de contenido](../assets/analisis_tipos_contenido_ent2.png){ width="680" }

Analisis de tendencias temporales:
![Analisis de tendencias temporales](../assets/analisis_tendencias_temporales_ent2.png){ width="680" }

Analisis de paises con mas visualizaciones:
![Analisis de paises con mas visualizaciones](../assets/analisis_paises_ent2.png){ width="680" }

Analísis de ratings y géneros:
![Analísis de ratings y géneros](../assets/analisis_ratings_generos_ent2.png){ width="680" }

Dashboard interactivo:
![Dashboard interactivo](../assets/dashboard_interactivo_ent2.png){ width="680" }

Análisis avanzado de géneros:
![Análisis avanzado de géneros](../assets/analisis_avanzado_generos_ent2.png){ width="680" }


## Reflexión
Cruzar catálogo con métricas de consumo (horas vistas/usuarios), ratings externos y datos financieros permitiría estimar ROI por género/país y priorizar inversión.  
Agregar benchmark con competidores daría contexto estratégico.

### Preguntas a responder luego de los análisis
1.  ¿Qué tipo de visualización es más efectiva para mostrar distribuciones temporales? 💡 PISTA: Compara line plot vs area plot vs bar plot

2. ¿Por qué usamos diferentes tipos de gráficos para diferentes datos? 💡 PISTA: 🔗 Guía de tipos de gráficos

3. ¿Qué insights de negocio obtuviste que Netflix podría usar? 💡 PISTA: Piensa en estrategias de contenido, mercados objetivo, tipos de producción

4. ¿Cuál fue la visualización más reveladora y por qué? 💡 PISTA: ¿Qué patrón no esperabas ver?

5. ¿Cómo mejorarías este análisis con más datos? 💡 PISTA: Datos de audiencia, ratings de IMDb, presupuestos, etc.


### Respuestas 
1. La visualización más clara es el area plot, ya que permite apreciar de manera directa y efectiva la variación de los datos a lo largo del tiempo. En segundo lugar se encuentra el line plot, que también refleja bien la tendencia, aunque con menor impacto visual. Finalmente, el bar plot resulta menos adecuado, dado que dificulta percibir la continuidad temporal de los datos.

2. La elección del tipo de gráfico depende directamente de lo que se busque resaltar en el análisis. En otras palabras, ciertas visualizaciones se adaptan mejor a determinados objetivos de comunicación y, por lo tanto, es recomendable seleccionarlas en función del mensaje que se desea transmitir. Por ejemplo, los heatmaps resultan especialmente útiles para detectar patrones, zonas de interés, puntos de fricción u oportunidades de mejora. A esto se suma la influencia de la psicología del color, ya que la escala cromática elegida puede modificar la interpretación del observador. Un heatmap en tonos rojos o cálidos, por ejemplo, suele asociarse a algo negativo o problemático en la medida que aumenta la intensidad del color, mientras que una escala en tonos fríos puede transmitir la percepción opuesta.

3. Considero que estos serían unos buenos puntos en los que netflix podría intentar avanzar:
   
    Estrategia de contenido por tipo:
    El catálogo refleja un crecimiento marcado de las series a partir de 2017, lo que indica que Netflix debería continuar apostando por producciones seriadas, ya que han sido motor clave de su expansión.
    En cuanto a las películas, aunque mantienen relevancia, su crecimiento es más estable; en este caso, la estrategia más conveniente sería concentrarse en grandes producciones (blockbusters) en géneros de alta demanda.

    Mercados objetivo:
    Estados Unidos e India concentran la mayor parte de los títulos, lo que evidencia una fuerte dependencia de estos mercados. Para mitigar riesgos y ampliar su base de suscriptores, Netflix debería diversificar sus producciones locales en mercados emergentes como Latinoamérica, África y Europa del Este.

    Géneros predominantes:
    Dramas, comedias y películas internacionales lideran el catálogo. Esto sugiere la conveniencia de reforzar géneros universales, pero también de explotar nichos con menor saturación, como el anime, los documentales o los realities.

4. La visualización de la evolución de títulos por año (Movies vs. TV Shows) resultó la más significativa. El hallazgo clave fue el aumento abrupto de series a partir de 2017, superando el crecimiento de películas.
Este cambio confirma una transformación en la estrategia de Netflix: pasó de ser un distribuidor principalmente de películas a consolidarse como productor de contenido seriado original, lo cual fue determinante para diferenciarse de competidores como HBO o Amazon Prime.

5. Posibles mejoras con más datos

    Audiencia real: cruzar el catálogo con métricas de consumo (horas vistas, número de usuarios únicos) permitiría verificar si los géneros con mayor oferta son también los más demandados.

    Calidad percibida: integrar ratings externos (IMDb, Rotten Tomatoes) posibilitaría evaluar la calidad frente al volumen de producción.

    Datos financieros: considerar presupuestos y retornos estimados ayudaría a calcular el ROI por género o país, información valiosa para definir prioridades de inversión.

    Análisis competitivo: incorporar datos de catálogos de Prime, Disney+, entre otros, permitiría un benchmark para identificar ventajas y debilidades frente a la competencia.
   
## Referencias
- Dataset: *Netflix Titles* (Kaggle)  
- Documentación de visualización en Python
