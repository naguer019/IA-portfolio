---
title: "📊⚙️ Preparación y Enriquecimiento de Datos en Cloud Dataprep"
date: 2025-12-05
---

# 📊⚙️ Preparación y Enriquecimiento de Datos en Cloud Dataprep

## Contexto

En esta actividad se completó el lab intermedio **"Creating a Data Transformation Pipeline with Cloud Dataprep" (GSP430)** de Google Cloud Skills Boost. Este lab brindó experiencia práctica con **Cloud Dataprep (Alteryx Designer Cloud)**, una herramienta visual para explorar, limpiar y transformar datos antes de análisis o carga en data warehouses. Se construyó un pipeline completo que toma datos desde BigQuery, los procesa en Dataprep, y publica los resultados nuevamente en BigQuery.


**Fuente**: [Creating a Data Transformation Pipeline with Cloud Dataprep (GSP430)](https://www.skills.google/focuses/4415)  
**Nivel:** Intermedio  
**Duración:** 75 minutos

---

## Objetivos

- Entender la interfaz y funcionalidades de Cloud Dataprep  
- Conectar datasets de BigQuery como origen y destino  
- Explorar calidad de datos con herramientas visuales  
- Construir un pipeline de limpieza y enriquecimiento  
- Crear columnas calculadas y transformaciones complejas  
- Ejecutar un job de Dataprep usando Dataflow  
- Exportar resultados a BigQuery  
- Comprender el flujo end-to-end BigQuery → Dataprep → BigQuery

---

## Actividades (con tiempos estimados)

| Actividad                          | Tiempo | Resultado esperado |
|-----------------------------------|:------:|-------------------|
| Configuración inicial             | 10m    | Dataprep habilitado |
| Creación de dataset en BigQuery   | 10m    | Dataset `ecommerce` creado |
| Conexión BigQuery → Dataprep      | 10m    | Flow configurado |
| Exploración de datos              | 15m    | Quality profiling realizado |
| Limpieza de datos                 | 15m    | Recipe con transformaciones |
| Enriquecimiento                   | 15m    | Columnas calculadas |
| Ejecución del pipeline            | 10m    | Tabla final en BigQuery |

---

## Desarrollo

### 1. Configuración Inicial de Cloud Dataprep

#### Habilitación del servicio

Se creó la identidad necesaria para Dataprep:

```bash
gcloud beta services identity create --service=dataprep.googleapis.com
````

Luego se accedió al servicio desde:

**Navigation Menu → Analytics → Alteryx Designer Cloud**

Incluyendo aceptación de términos, permisos, autenticación con Qwiklabs y configuración del bucket de almacenamiento.

*Nota:* Cloud Dataprep requiere Google Chrome.

---

## 2. Creación de Dataset en BigQuery

Se creó el dataset `ecommerce` y luego la tabla base con:

```sql
CREATE OR REPLACE TABLE ecommerce.all_sessions_raw_dataprep AS
SELECT *
FROM `data-to-insights.ecommerce.all_sessions_raw`
WHERE date = '20170801'; 
```

Esto generó ~56.000 filas del Google Merchandise Store.

---

### 3. Conexión de BigQuery a Cloud Dataprep

Pasos:

1. Crear Flow → `Ecommerce Analytics Pipeline`
2. Añadir dataset desde BigQuery
3. Seleccionar tabla `all_sessions_raw_dataprep`

Dataprep analiza estructura, tipos y calidad.

---

### 4. Exploración Visual de Datos

Dataprep permite:

#### Panel de esquema

* Columnas y tipos
* Detección automática de inconsistencias

#### Vista tabular

* Histogramas por columna
* Outliers y valores faltantes

#### Panel de sugerencias

* Transformaciones recomendadas

#### Hallazgos clave

* Varias columnas nulas
* Revenue multiplicado por 1e6
* Tipos de hit diversos
* Datos de sesión complejos

---

### 5. Limpieza de Datos

#### 5.1 Filtrar por `type = "PAGE"`

Se seleccionaron solo visualizaciones de páginas.

#### 5.2 Quitar columnas irrelevantes

Columnas nulas, redundantes o no útiles para análisis.

---

### 6. Enriquecimiento de Datos

#### 6.1 Crear identificador único

Se creó `unique_session_id` concatenando:

```
fullVisitorId + "-" + visitId
```

#### 6.2 Etiquetas descriptivas de acciones e-commerce

`eCommerceAction_type` (0–8) → `eCommerceAction_label`
Creado mediante **Case Statement**.

#### 6.3 Normalizar revenue

```
DIVIDE(totalTransactionRevenue, 1000000)
```

Nueva columna: `totalTransactionRevenue1` → tipo Decimal.

---

### 7. Ejecución del Pipeline en Dataflow

Configuración:

* Ambiente: **Dataflow + BigQuery**
* Crear nueva tabla
* Nombre destino: `revenue_reporting`
* Opción: *Drop table on every run*

Proceso:

1. Validación
2. Compilación a Apache Beam
3. Ejecución distribuida
4. Escritura en BigQuery

Verificación final: tabla `revenue_reporting` disponible y correcta.

---

## Conceptos Clave Aprendidos

### Cloud Dataprep

* Preparación visual de datos
* Detección automática de problemas
* Transformaciones sin código
* Integración nativa con BigQuery

### Recipes y Flows

* Flow: pipeline completo
* Recipe: pasos de transformación
* Dataset: origen conectado

### Tipos de transformaciones

* Filtrado
* Eliminación de columnas
* Enriquecimiento
* Case statements
* Fórmulas personalizadas
* Joins, pivots y agregaciones

### BigQuery ↔ Dataprep

* Lectura/escritura directa
* Ideal para analítica y warehousing

### Dataflow

* Escalabilidad
* Paralelización
* Monitoreo y logging de ejecución

---

## Aplicaciones Prácticas y Usos Futuros

### Machine Learning

* Preparar features
* Crear datasets limpios
* Manejar valores faltantes

### ETL para Data Warehousing

* Pipelines visuales
* Reglas de negocio documentadas
* Integración con BigQuery

### Google Analytics

* Limpieza
* Mapeos descriptivos
* Reporting avanzado

### Data Quality

* Perfilado de columnas
* Validación de valores
* Auditorías rápidas

### Colaboración

* No-code para analistas
* Documentación visual
* Revisión colaborativa de transformaciones

---

## Desafíos y Observaciones

#### Interfaz abrumadora al inicio

Solución: seguir el flujo básico Flow → Recipe → Transformaciones.

#### Necesidad de usar Chrome

Limitación técnica del servicio.

#### Transformaciones complejas

Case statements requieren planificación previa.

#### Jobs lentos

Utilizar subsets para desarrollo.

#### Debugging

Revisar paso a paso con vista previa.

---

## Reflexiones Finales

### Fortalezas

* Pipeline end-to-end
* Datos reales
* Exploración guiada
* Integración fluida con BigQuery

### Áreas de mejora

* Tiempo limitado
* Dataset pequeño
* Pocos ejemplos avanzados

### Comparación

* SQL: rápido para tareas simples, menos visual
* Python ETL: más flexible, menos accesible
* Dataflow manual: más control, más complejo

### Valor para Ingeniería de Datos

* Herramienta esencial para exploración
* Acelera prototipos
* Ideal para equipos mixtos
* Excelente para preparación previa a ML

---

## Decisiones y Próximos Pasos

### Decisiones tomadas

* Filtrar `PAGE`
* Crear ID único
* Mapeos descriptivos
* Normalizar revenue
* Sobrescribir tabla en cada corrida

### Próximos pasos

* Usar datasets grandes
* Probar joins y pivots
* Integrar Cloud Storage
* Automatizar pipelines
* Completar labs avanzados

---

## Conclusiones

Este lab demuestra el ciclo completo de preparación de datos usando Google Cloud: BigQuery → Dataprep → Dataflow → BigQuery. La preparación visual acelera el análisis, mejora la calidad de datos y facilita la colaboración.

**Takeaways clave:**

* La preparación visual puede ser tan poderosa como el código
* Explorar antes de transformar es esencial
* GCP ofrece integración nativa y fluida
* Las herramientas visuales aumentan la colaboración
* Dataprep es ideal para prototipado y limpieza intensiva de datos

---

## Referencias

* Google Cloud Skills Boost
* Lab GSP430: Creating a Data Transformation Pipeline with Cloud Dataprep
* Cloud Dataprep Docs
* Alteryx Designer Cloud
* BigQuery Docs
* Dataflow Docs
