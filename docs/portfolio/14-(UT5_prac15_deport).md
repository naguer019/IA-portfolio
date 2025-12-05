---
title: "🔁 Del Código al Flujo: Cómo Prefect Reinventa el ETL Moderno"
date: 2025-12-05
---

# 🔁 Del Código al Flujo: Cómo Prefect Reinventa el ETL Moderno

## Contexto

En esta actividad se trabajó con **Prefect**, una herramienta moderna de orquestación de pipelines de datos, diseñando e implementando un mini pipeline ETL completo.
El objetivo fue investigar la documentación oficial para comprender los conceptos fundamentales (**Tasks, Flows, DAGs implícitos, caching**) y explorar funcionalidades avanzadas como **retries, logging estructurado, validación de datos y deployments**.

A partir de esto, se construyó un pipeline ETL para procesar datos de ventas de un e-commerce, aplicando principios de **DataOps** como observabilidad, reproducibilidad y CI/CD para datos.

**Fuente**: [Tarea 14 (15 en la página) - Prefect](https://juanfkurucz.com/ucu-id/ut5/15-etl-dataops-prefect/)

---

## Objetivos

* Comprender los conceptos fundamentales de Prefect: **Tasks, Flows, estados y caching**.
* Investigar y aplicar funcionalidades avanzadas: **retries, logging estructurado, validación**.
* Diseñar e implementar un **pipeline ETL completo** con Prefect.
* Explorar **deployments y scheduling** para ejecución programada.
* Conectar Prefect con principios de **DataOps**: observabilidad, reproducibilidad, CI/CD.
* Comparar Prefect con alternativas como **Apache Airflow** y **Dagster**.

---

## Actividades (con tiempos estimados)

| Actividad                                | Tiempo | Resultado esperado                                           |
| ---------------------------------------- | :----: | ------------------------------------------------------------ |
| Investigación: Conceptos fundamentales   |   15m  | Respuestas sobre Tasks, Flows, Results y Caching             |
| Diseño conceptual del escenario          |   5m   | Escenario definido: e-commerce con roles identificados       |
| Implementación del pipeline base         |   20m  | Pipeline ETL funcionando: extract, transform, load           |
| Investigación: Funcionalidades avanzadas |   15m  | Retries, caching, logging, concurrencia investigados         |
| Investigación: Deployments y Scheduling  |   10m  | Conceptos de deployment, work pools, scheduling comprendidos |
| Extensión DataOps                        |   15m  | Validación con logging estructurado implementada             |
| Reflexión y conexión con DataOps         |   5m   | Reflexiones sobre observabilidad, reproducibilidad y CI/CD   |

---

## Desarrollo

### 1. Investigación: Conceptos Fundamentales de Prefect

#### 1.1 Tasks en Prefect

**¿Qué es una Task?**
Una Task en Prefect es una unidad de trabajo individual que representa una operación específica dentro de un pipeline.
Según la [documentación oficial](https://docs.prefect.io/latest/concepts/tasks/), una task es *“a discrete piece of work that can be tracked and retried independently”*.
En la práctica, son funciones Python decoradas con `@task` que encapsulan lógica concreta (extraer datos, transformar, cargar, validar, etc.).

**Evaluación diferida ("lazy evaluation")**
Las tasks no se ejecutan inmediatamente cuando se definen o llaman. Prefect primero construye una representación del grafo de dependencias y la ejecución real ocurre al correr el **flow** completo.
Esto permite:

* Optimizar la ejecución.
* Resolver dependencias de forma automática.
* Manejar DAGs complejos sin tener que declararlos manualmente.

**Estados de las Tasks**
Prefect rastrea el estado de cada task durante todo su ciclo de vida:

* `PENDING`: Esperando ejecución.
* `RUNNING`: En ejecución.
* `COMPLETED`: Finalizada con éxito.
* `FAILED`: Falló durante la ejecución.
* `RETRYING`: Reintentando después de un fallo.
* `CANCELLED`: Cancelada antes de terminar.
* `CRASHED`: Fallo inesperado (error no controlado).

Esto permite tener **observabilidad fina** sobre qué pasó en cada paso del pipeline.

**Parámetros importantes del decorador `@task`**

* `retries`: Número de reintentos si falla.
* `retry_delay_seconds`: Tiempo entre reintentos.
* `timeout_seconds`: Tiempo máximo de ejecución.
* `cache_key_fn`: Función personalizada para generar la clave de caché.
* `cache_expiration`: Duración de validez del caché.
* `tags`: Etiquetas para organización y filtrado.
* `log_prints`: Captura automática de `print` como logs estructurados.

---

#### 1.2 Flows en Prefect

**Diferencias entre Flow y Task**

* Una **Task** es una unidad de trabajo puntual.
* Un **Flow** es el *contenedor* que orquesta y coordina la ejecución de múltiples tasks, definiendo un flujo de trabajo completo.

Necesitamos ambos porque:

* Las **Tasks** permiten modularizar y reutilizar lógica.
* El **Flow** se encarga de las dependencias y de la orquestación global.

**Subflows**
Un **subflow** es un Flow que se llama desde otro Flow, útil para:

* Modularidad.
* Reutilización de partes del pipeline.
* Testing más simple.
* Separación lógica en subprocesos (por ejemplo, `extract_and_validate()` como subflow reutilizable).

**DAGs implícitos**
En Prefect, el DAG no se declara de forma explícita como en Airflow.
Cuando se pasa el resultado de una task como parámetro a otra (`t2(t1())`), Prefect detecta la dependencia y construye **el DAG de manera implícita**, definiendo el orden correcto de ejecución.

---

#### 1.3 Results y Caching

**Result persistence**
Prefect puede guardar y recuperar resultados de tasks. Esto es importante porque:

* Evita re-ejecutar tasks costosas.
* Facilita el debugging (se pueden inspeccionar resultados intermedios).
* Mejora la resiliencia (si falla algo, no hay que recomputar todo).
* Ayuda a la reproducibilidad y auditoría.

**Caching**
Permite evitar re-ejecuciones cuando las entradas no cambiaron. Se puede configurar:

* Usando una clave de caché automáticamente generada por Prefect a partir de los parámetros.
* Definiendo `cache_expiration` (por cuánto tiempo es válido el resultado).
* Personalizando la clave con `cache_key_fn`.

**`cache_key_fn`**
Sirve para:

* Cachear en función de solo algunos parámetros.
* Implementar lógica de invalidación de caché basada en reglas de negocio (por ejemplo, fecha de actualización de una tabla).

---

### 2. Diseño Conceptual

**Escenario elegido**:
Ventas de un e-commerce con datos de transacciones diarias.

**Roles y arquitectura del escenario:**

* **Business Data Owner**: Equipo de ventas/negocios que genera las transacciones.
* **Data Engineers**: Construyen y mantienen el pipeline ETL.
* **Data Consumers**: Analistas, científicos de datos y dashboards que consumen los datos procesados.

**Tipo de pipeline**: **Batch**

**Justificación**:

* Las ventas se procesan en lotes diarios.
* No se requiere tiempo real para el caso inicial.
* El batch es eficiente para análisis agregados y facilita:

  * validación,
  * control de errores,
  * auditoría de cada corrida.

---

### 3. Implementación del Pipeline Base

#### 3.1 Setup

Se configuró el entorno con:

* `prefect` (para flows y tasks)
* `pandas`, `numpy`
* `datetime` y utilidades estándar de Python

Imports clave:

```python
from prefect import flow, task
import pandas as pd
import numpy as np
from datetime import datetime
```

---

#### 3.2 Tasks Implementadas

**Task 1: Extract — `extract_data`**

* Decorador: `@task(tags=["extract", "data-source"], log_prints=True)`
* Función: simula extracción de datos de ventas generando 100 registros con:

  * fecha,
  * producto,
  * cantidad,
  * precio_unitario,
  * región.
* Uso de tags para organización y `log_prints=True` para registrar mensajes.

**Task 2: Transform — `transform_data`**

* Decorador: `@task(tags=["transform", "data-processing"], log_prints=True)`
* Transformaciones principales:

  * Cálculo de `total = cantidad * precio_unitario`.
  * Categorización de `total` en bandas de ticket (Bajo, Medio, Alto, Muy Alto).
  * Agregado de columnas de **mes** y **día de la semana** para análisis temporal.

**Task 3: Load — `load_data`**

* Decorador: `@task(tags=["load", "data-output"], log_prints=True, retries=2, retry_delay_seconds=3)`
* Función: guarda los datos transformados en un archivo CSV.
* Incluye **reintentos automáticos** en caso de fallo de escritura (I/O).

---

#### 3.3 Flow Principal

**Flow: `etl_flow()`**

* Decorador: `@flow(name="ETL Pipeline Ventas", log_prints=True)`
* Pasos:

  1. `df_raw = extract_data()`
  2. `df_transformed = transform_data(df_raw)`
  3. `load_data(df_transformed)`

Prefect construye automáticamente el DAG:

`extract_data → transform_data → load_data`

Esto muestra en la práctica cómo las dependencias se definen *por el flujo de datos* y no por declaraciones explícitas.

---

### 4. Investigación: Funcionalidades Avanzadas

#### 4.1 Retries y manejo de errores

Usando `@task(retries=N, retry_delay_seconds=X)` se pueden configurar reintentos automáticos para tareas que fallan esporádicamente (APIs, conexiones, escritura en disco, etc.).
En el pipeline, `load_data()` utiliza 2 reintentos con 3 segundos de espera.

---

#### 4.2 Caching de resultados

Permite evitar re-ejecutar tasks costosas cuando los parámetros de entrada no cambiaron.
Se configura con:

* `cache_expiration` (ej: `timedelta(hours=1)`).
* `cache_key_fn` para personalizar la clave de caché.

Es especialmente útil para:

* Extracciones de APIs.
* Cálculos pesados.
* Etapas intermedias costosas de un pipeline.

---

#### 4.3 Logging personalizado

Se puede usar:

* `get_run_logger()` para logs estructurados.
* `log_prints=True` para capturar `print()` automáticamente.

Ventajas:

* Logs centralizados.
* Integración con la UI de Prefect.
* Facilita diagnóstico y auditoría en DataOps.

---

#### 4.4 Concurrencia y paralelismo

Prefect soporta ejecución concurrente con `ConcurrentTaskRunner()` en el flow.
Las tasks independientes se pueden ejecutar en paralelo usando `.submit()`.

Ejemplo conceptual:

* Procesar una task `transform_region(region)` para cada región en paralelo.
* Reducir el tiempo total de una corrida batch.

---

### 5. Investigación: Deployments y Scheduling

#### 5.1 Conceptos de Deployment

**Deployment**
Es la configuración que permite ejecutar un Flow de manera programada o bajo demanda. Conecta:

* el código del Flow
* con la configuración de ejecución (scheduling, work pool, parámetros).

Mientras que el **Flow** es el código Python, el **Deployment** es la “instalación” del Flow en Prefect, con metadatos de ejecución.

**Work Pool**

* Grupo de recursos/entornos donde se ejecutan los flows.
* Permite organizar ejecuciones en distintos entornos (local, servidor, cloud).

**Worker**

* Proceso que se conecta a un Work Pool y toma trabajos (flows) para ejecutarlos.
* La cadena es: `Deployment → Work Pool → Worker → Ejecución del Flow`.

---

#### 5.2 Scheduling

**Tipos de schedules** más comunes:

* `CronSchedule`: usando sintaxis cron (`"0 6 * * *"` → todos los días a las 6:00).
* `IntervalSchedule`: ejecución cada cierto intervalo (`timedelta(hours=1)`).
* `RRuleSchedule`: reglas más complejas (RFC 5545), útil para calendarios de negocio.

**Ejemplo cron**
`"0 6 * * *"` → todos los días a las 6 AM.
Formato: `minuto hora día_mes mes día_semana`.

**RRuleSchedule**
Más flexible, permite definir patrones como “lunes y miércoles a las 9 AM, excepto feriados”.

---

### 6. Extensión DataOps: Validación con Logging Estructurado

Se implementó la **Opción A – Validación con logging estructurado**, clave para DataOps.

**Task de validación: `validate_data()`**

* Decorador:
  `@task(retries=1, retry_delay_seconds=2, tags=["validation", "data-quality"], log_prints=True)`
* Usa `get_run_logger()` para registrar mensajes de validación.

**Validaciones realizadas:**

1. **DataFrame no vacío**.
2. **Valores nulos**: se registran como *warning*.
3. **Columnas requeridas** (`fecha`, `producto`, `cantidad`, `precio_unitario`, `total`).
4. **Tipos de datos correctos** (ej: `total` numérico).
5. **Valores negativos** en `cantidad` detectados como *warning*.

**Niveles de log:**

* `info`: validaciones exitosas e información general.
* `warning`: problemas no críticos (nulos, valores atípicos).
* `error`: problemas críticos que deben detener el pipeline.

El flow actualizado (`etl_flow_with_validation()`) ejecuta la validación **entre extracción y transformación**, asegurando que solo se procese información de calidad.

---

### 7. Reflexión y Conexión con DataOps

#### 7.1 Prefect y principios de DataOps

**Observabilidad**

Prefect ofrece observabilidad a través de:

* Estados detallados por task y por flow.
* Logs estructurados centralizados.
* Métricas de tiempo, tasas de éxito/fallo.
* UI con monitoreo en tiempo real.
* Posibilidad de alertas y notificaciones.

**Reproducibilidad**

* El caching y la persistencia de resultados ayudan a obtener resultados consistentes.
* Se pueden comparar outputs actuales con resultados previos cacheados.
* Facilita reproducir corridas anteriores para auditoría.

**CI/CD para datos**

* Los Deployments permiten versionar y desplegar flows.
* Los schedules automatizan ejecuciones recurrentes.
* Es posible tener distintos ambientes (dev / staging / prod).
* Se integra con pipelines de CI/CD (por ejemplo, GitHub Actions, GitLab CI).

---

#### 7.2 Comparación con Alternativas

**Prefect vs Apache Airflow**

1. **Filosofía de diseño**

   * Airflow usa DAGs explícitos y *operators*.
   * Prefect usa DAGs implícitos con código Python más natural.

2. **Curva de aprendizaje**

   * Prefect es más directo: solo decoradores `@task` y `@flow`.
   * Airflow requiere entender DAGs, Operators, XComs, etc.

3. **Experiencia de desarrollo**

   * Prefect es más “Pythonic” y menos verboso.
   * Airflow sigue muy presente en entornos legacy y Big Data clásicos.

**Prefect vs Dagster**

* **Enfoque**

  * Dagster es más *data-centric* (assets como primitivos principales).
  * Prefect es más *workflow-centric* (tasks y flows).

* **Integración**

  * Dagster integra fuerte con ecosistema de datos (pandas, Spark, etc.).
  * Prefect tiene un ecosistema más amplio y una comunidad mayor hoy en día.

Ambas son alternativas modernas a Airflow; la elección depende de si el equipo piensa más en términos de **datos** (Dagster) o de **flujos de trabajo** (Prefect).

---

## Evidencias

* Notebook de implementación: `docs/portfolio/UT5/Notebooks/quince.ipynb`.
* Pipeline ETL completo funcionando con Prefect.
* Task de validación con **logging estructurado** integrada en el flow.
* Respuestas de investigación basadas en documentación oficial de Prefect.

---

## Resultados

En esta actividad se logró:

1. Implementar un **pipeline ETL completo** para un escenario de e-commerce.
2. Investigar y aplicar funcionalidades avanzadas de Prefect (retries, caching, logging, deployments).
3. Incorporar una **extensión DataOps** con validación y logging estructurado.
4. Conectar Prefect con principios de **observabilidad, reproducibilidad y CI/CD de datos**.
5. Comparar Prefect con herramientas alternativas (Airflow, Dagster) entendiendo su rol en el ecosistema.

---

## Aprendizajes Clave

### Conceptos fundamentales

* Diferenciar entre **Task** (unidad de trabajo) y **Flow** (orquestación).
* Entender cómo Prefect construye **DAGs implícitos** a partir de dependencias de datos.
* Ver el valor de los **estados de ejecución** y del logging para observabilidad.

### Funcionalidades avanzadas

* Los **retries** permiten robustecer el pipeline frente a errores intermitentes.
* El **logging estructurado** facilita muchísimo el debugging en escenarios reales.
* El **caching** apunta tanto a eficiencia como a reproducibilidad.

### DataOps

* Prefect habilita prácticas de DataOps al ofrecer:

  * observabilidad,
  * versionado,
  * programación recurrente,
  * integración con CI/CD.

---

## Desafíos y Soluciones

### Desafío 1: Entender DAGs implícitos

**Problema**: al principio cuesta entender cómo se construye el DAG sin declararlo.
**Solución**: experimentar pasando resultados de una task a otra y observar en la UI de Prefect la estructura generada.

---

### Desafío 2: Diseño de validaciones útiles

**Problema**: encontrar el equilibrio entre demasiadas validaciones (ruido) y pocas (falta de control).
**Solución**: usar diferentes niveles de logging (`info`, `warning`, `error`) y centrarse primero en columnas clave y tipos de datos críticos.

---

### Desafío 3: Diferenciar Flows de Deployments

**Problema**: confusión inicial entre el código del flow y su configuración de ejecución.
**Solución**: pensar el **Flow** como el *código Python* y el **Deployment** como la *configuración de “cómo, cuándo y dónde” se ejecuta*.

---

## Decisiones y Próximos Pasos

### Decisiones tomadas

1. Usar un **escenario de e-commerce** simple pero realista.
2. Empezar con pipeline **batch diario** en lugar de streaming.
3. Elegir la extensión DataOps de **validación con logging** por ser más crítica para calidad de datos que la concurrencia en este primer ejemplo.
4. Registrar logs estructurados en todas las tasks para maximizar observabilidad.

### Próximos pasos

* Probar Prefect en entorno **Cloud/Server**.
* Ampliar el set de validaciones con herramientas dedicadas (Great Expectations, Pydantic).
* Implementar **concurrencia por región** o por fuente de datos.
* Definir un **Deployment real** con scheduling (por ejemplo, diario 06:00).
* Integrar el pipeline con CI/CD (GitHub Actions u otro).
* Configurar persistencia de resultados en almacenamiento externo (S3, GCS).
* Añadir alertas ante fallos o degradación de calidad de datos.

---

> *"La mejor forma de aprender una herramienta es leer su documentación oficial.
> Los tutoriales te dan el 'qué'; la documentación, el 'por qué' y el 'cómo'."*

---

## Referencias

* [Documentación oficial de Prefect](https://docs.prefect.io/)
* [Prefect Concepts Overview](https://docs.prefect.io/latest/concepts/)
* [Prefect Tasks](https://docs.prefect.io/latest/concepts/tasks/)
* [Prefect Flows](https://docs.prefect.io/latest/concepts/flows/)
* [Prefect Caching](https://docs.prefect.io/latest/concepts/tasks/#caching)
* [Prefect Deployments](https://docs.prefect.io/latest/concepts/deployments/)
