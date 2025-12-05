---
title: "🔓☁️ Google Cloud Desbloqueado: Mi Primera Experiencia con Hands-on Labs"
date: 2025-12-05
---

# 🔓☁️ Google Cloud Desbloqueado: Mi Primera Experiencia con Hands-on Labs

## Contexto

En esta actividad se completó el lab introductorio **"A Tour of Google Cloud Hands-on Labs" (GSP282)** de Google Cloud Skills Boost.  
Este lab está pensado como **puerta de entrada para principiantes** en Google Cloud Platform, ofreciendo una experiencia práctica guiada con:

- La **Cloud Console**  
- La plataforma de labs **Qwiklabs / Google Cloud Skills Boost**  
- Conceptos básicos de **proyectos**, **IAM** y **habilitación de APIs**

El foco estuvo en aprender a moverse dentro del entorno de labs, entender cómo se crea un **proyecto temporal** para cada práctica y realizar tareas simples de administración.

**Fuente**: [Google Cloud Skills Boost - Lab GSP282](https://www.skills.google/focuses/2794?parent=catalog)
**Nivel**: Introductorio  
**Duración estimada del lab**: 45 minutos  

---

## Objetivos

- Familiarizarse con la interfaz de **Google Cloud Skills Boost** y **Qwiklabs**.  
- Acceder a la **Cloud Console** usando credenciales temporales del lab.  
- Explorar y entender el concepto de **proyecto** en Google Cloud.  
- Revisar y modificar **roles y permisos básicos** usando **Cloud IAM**.  
- Habilitar **APIs y servicios** en un proyecto de Google Cloud.  
- Comprender la estructura y los componentes comunes de los labs de Google Cloud.

---

## Actividades (con tiempos estimados)

| Actividad                                | Tiempo | Resultado esperado                                                            |
|-----------------------------------------|:------:|-------------------------------------------------------------------------------|
| Acceso a Cloud Console                  |  10m   | Sesión iniciada en Cloud Console con credenciales temporales                 |
| Exploración de proyectos                |  10m   | Comprensión de Project ID y organización de recursos                         |
| Revisión de roles y permisos (IAM)      |  15m   | Entendimiento de roles básicos (Viewer, Editor, Owner) y gestión de acceso   |
| Habilitación de APIs                    |  10m   | Dialogflow API habilitada y comprensión de la API Library                     |

---

## Desarrollo

### 1. Fundamentos de Labs en Google Cloud Skills Boost

Antes de entrar en la Cloud Console se revisaron los **componentes estándar** de los labs:

- **Start Lab**:  
  Botón que crea un **ambiente temporal de Google Cloud** con un proyecto, permisos y credenciales provisionales. También inicia un **temporizador** que define cuánto dura el entorno.

- **Créditos**:  
  Unidad de “costo” del lab. En general, **1 crédito ≈ 1 USD**. Algunos labs básicos (como este) son gratuitos, mientras que los más avanzados consumen créditos por usar recursos más pesados.

- **Tiempo**:  
  Indica la **ventana disponible** para completar el lab. Al llegar a `00:00:00`, el entorno y los recursos se destruyen.

- **Score / Activity Tracking**:  
  Sistema de puntuación que verifica automáticamente si se cumplieron ciertos pasos. Solo completando todas las actividades se obtiene el **crédito de finalización**.

#### Plataforma Qwiklabs / Google Cloud Skills Boost

Se repasó también el rol de **Qwiklabs** como plataforma subyacente:

- Provee **labs guiados**, rutas de aprendizaje y desafíos prácticos.  
- Permite **rastrear el progreso**, obtener **badges** y construir un perfil de habilidades en la nube.  
- Facilita experimentar con GCP sin usar cuentas personales ni facturación propia, gracias a los **entornos temporales**.

---

### 2. Acceso a Cloud Console

#### Panel de detalles del lab

Al iniciar el lab, el panel **Lab details** mostró:

- Botón **“Open Google Cloud console”** para abrir la consola web.  
- **Username** y **Password** temporales (del tipo `student-xx-xxxxxx@qwiklabs.net`).  
- Un **Project ID** único asociado al lab.

Estos datos permiten entrar a un proyecto de Google Cloud aislado, sin tocar proyectos personales.

> **Buena práctica**: Usar siempre **ventana de navegación privada/incógnito** para evitar mezclar sesiones personales con las credenciales temporales del lab.

#### Proceso de inicio de sesión

Pasos realizados:

1. Clic en **“Open Google Cloud console”**.  
2. Inicio de sesión con las credenciales temporales provistas.  
3. Aceptación de términos y condiciones.  
4. Acceso a la interfaz principal de la **Cloud Console**, donde se gestionan recursos, APIs, IAM, etc.

---

### 3. Exploración de Proyectos en Google Cloud

#### Concepto de proyecto

Un **Google Cloud Project** es la unidad básica de organización en GCP. Cada proyecto agrupa:

- **Recursos y servicios** (VMs, bases de datos, buckets, etc.).  
- Configuración de **seguridad**, **IAM** y **facturación**.  
- Políticas y parámetros comunes para todo lo que vive dentro del proyecto.

Esto permite aislar ambientes (por ejemplo, `dev`, `staging`, `prod`) y organizar costos, permisos y límites.

#### Project ID vs Project Name

Se revisó la diferencia entre:

- **Project Name**:  
  Nombre legible por humanos, puede cambiarse.

- **Project ID**:  
  Identificador **único e inmutable a nivel global**. Es el que se usa en scripts, APIs, y comandos `gcloud`.

**Aclaraciones importantes**:

- El **Project ID no se puede cambiar** una vez creado.  
- Dos proyectos en todo GCP **no pueden compartir el mismo Project ID**.  
- Un proyecto es una “caja organizativa” de recursos, no un servicio individual.

#### Navegación en Cloud Console

Se exploró el menú lateral para identificar las grandes familias de servicios:

- **Compute**: Compute Engine, Cloud Functions, App Engine.  
- **Storage**: Cloud Storage, Cloud SQL, BigQuery.  
- **Networking**: VPC, Load Balancing, Cloud CDN.  
- **Security**: IAM, Cloud Identity.  
- **APIs & Services**: API Library, Service Accounts.  

Este recorrido ayudó a ubicar dónde se configuran los recursos clave en futuros labs de ingeniería de datos.

---

### 4. Revisión y Modificación de Roles y Permisos (IAM)

#### Cloud IAM: modelo de acceso

Se trabajó con **Cloud IAM (Identity and Access Management)**, que controla:

- **Principals**: usuarios, grupos, cuentas de servicio.  
- **Roles**: conjuntos de permisos.  
- **Permissions**: acciones específicas sobre recursos (leer, escribir, administrar).

Esto permite aplicar el **principio de menor privilegio**: dar a cada persona solo lo que necesita.

#### Roles básicos de proyecto

Se repasaron los **tres roles básicos**:

| Rol             | Descripción resumida                                                                                          |
|-----------------|---------------------------------------------------------------------------------------------------------------|
| **roles/viewer** | Solo lectura: ver recursos y datos, sin poder modificarlos.                                                   |
| **roles/editor** | Incluye Viewer + capacidad de crear y modificar recursos.                                                     |
| **roles/owner**  | Incluye Editor + gestión de permisos e IAM a nivel de proyecto, además de configuración de facturación.       |

#### Ejercicio práctico: otorgar un rol IAM

En la sección **IAM & Admin > IAM** se realizó:

1. Clic en **“Grant access”**.  
2. Ingreso de un usuario/principal.  
3. Asignación del rol **Viewer**.  
4. Guardado de cambios y verificación de que el usuario quedara listado con ese rol.

> **Aprendizaje clave**:  
> Aunque un **Editor** puede crear y modificar recursos, no necesariamente puede gestionar todos los miembros del proyecto. La gestión de acceso está más ligada al rol de **Owner** y a ciertos permisos de IAM.

---

### 5. Habilitación de APIs y Servicios

#### API Library

Google Cloud agrupa sus servicios como **APIs**. Antes de usarlas en un proyecto, deben ser explícitamente **habilitadas**.

Características:

- Más de **200 APIs** para temas como ML, datos, redes, seguridad, etc.  
- Cada una viene con **documentación, métricas de uso, errores y latencia**.  
- Se pueden consumir vía **REST**, **client libraries** o desde otras herramientas de GCP.

#### Ejercicio práctico: habilitar Dialogflow API

Pasos realizados:

1. Ir a **APIs & Services > Library**.  
2. Buscar **“Dialogflow”**.  
3. Seleccionar **Dialogflow API**.  
4. Leer la descripción (enfocada en aplicaciones conversacionales, chatbots, etc.).  
5. Hacer clic en **Enable**.  
6. Confirmar que la API quedó habilitada para el proyecto del lab.  
7. Opcionalmente, explorar la sección **“Try this API”** para ver ejemplos de uso.

> **Nota**:  
> En los labs, muchos proyectos vienen con varias APIs ya habilitadas para ahorrar tiempo.  
> En proyectos propios, esta habilitación se vuelve un paso obligatorio antes de consumir cualquier servicio.

#### Categorías principales de APIs

La API Library organiza servicios en categorías como:

- Machine Learning  
- Storage  
- Compute  
- Networking  
- Security & Identity  
- Big Data  
- IoT  

Esta organización facilita encontrar APIs relevantes según el dominio de trabajo.

---

## Conceptos Clave Aprendidos

### 1. Google Cloud Platform (GCP)

- Es una **suite de servicios en la nube** sobre la infraestructura de Google.  
- Ofrece recursos de **computación**, **almacenamiento**, **análisis de datos**, **ML**, redes, etc.  
- Todo se organiza a través de **proyectos**.

### 2. Proyectos de Google Cloud

- Agrupan recursos relacionados, permisos y facturación.  
- El **Project ID** es único e inmutable.  
- Permiten separar ambientes (dev/staging/prod) y controlar costos y seguridad.

### 3. Cloud IAM

- Modelo central de **seguridad y permisos** en GCP.  
- Usa roles y permisos para controlar quién puede hacer qué.  
- Es clave aplicar el **principio de menor privilegio** para evitar exposiciones innecesarias.

### 4. APIs y Servicios

- Cada servicio de GCP se expone como una **API**.  
- Las APIs deben **habilitarse** explícitamente en cada proyecto.  
- Google provee **librerías de cliente** para varios lenguajes, y métricas de uso para monitoreo.

### 5. Cloud Console

- Es la **interfaz web** central para administrar proyectos y servicios.  
- Organiza recursos por categorías lógicas.  
- Permite ver estado, logs, métricas y configuraciones desde un solo lugar.

---

## Aplicaciones Prácticas y Usos Futuros

### 1. Administración de Infraestructura

Lo aprendido sobre proyectos e IAM es base para:

- Separar ambientes (`dev`, `staging`, `prod`).  
- Asignar costos por proyecto o por equipo.  
- Aplicar políticas de seguridad y acceso consistentes.

### 2. Desarrollo de Aplicaciones

La habilitación de APIs y comprensión de la API Library permite:

- Integrar servicios como **Dialogflow**, Vision API, Translation API, etc.  
- Usar librerías de cliente para acortar el tiempo de desarrollo.  
- Monitorear el consumo de APIs y optimizar costos.

### 3. Gestión de Seguridad

Con IAM se podrá:

- Diseñar modelos de **RBAC** (role-based access control).  
- Dar permisos mínimos necesarios a cada rol.  
- Auditar accesos y detectar posibles configuraciones peligrosas.

### 4. Aprendizaje Continuo

Google Cloud Skills Boost ofrece:

- Labs prácticos para diferentes áreas (ML, Data Engineering, Security, DevOps).  
- Rutas de aprendizaje y badges verificables.  
- Contenido actualizado según las últimas funcionalidades de GCP.

---

## Desafíos y Observaciones

### Desafío 1: Navegación inicial en Cloud Console

- **Problema**: Hay muchos servicios y se puede sentir abrumador al principio.  
- **Solución**: Seguir el guión del lab y explorar el menú por secciones.  
- **Aprendizaje**: Con práctica, la organización por categorías (Compute, Storage, Networking, etc.) se vuelve natural.

### Desafío 2: Entender IAM en la práctica

- **Problema**: Los conceptos de principals, roles y permisos pueden ser abstractos.  
- **Solución**: El ejercicio de otorgar el rol **Viewer** y ver su efecto ayuda a fijar la idea.  
- **Aprendizaje**: Es más fácil comprender IAM cuando se ve el impacto concreto de los roles.

### Desafío 3: Concepto de “habilitar una API”

- **Problema**: Puede no estar claro por qué hay que “habilitar” un servicio antes de usarlo.  
- **Solución**: El lab muestra que habilitar una API es equivalente a “activar” ese servicio para el proyecto.  
- **Aprendizaje**: Este paso es obligatorio en proyectos reales y tiene impacto en facturación y seguridad.

---

## Reflexiones Finales

### Fortalezas del lab

1. **Enfoque introductorio bien guiado**  
   Presenta conceptos de manera gradual y con ejemplos concretos.

2. **Entornos temporales seguros**  
   Permite experimentar sin riesgo sobre proyectos personales.

3. **Aprendizaje hands-on**  
   La práctica inmediata ayuda más que leer teoría aislada.

4. **Activity tracking**  
   El sistema de puntuación da feedback claro sobre qué falta completar.

### Áreas de mejora

1. El tiempo limitado puede generar presión en principiantes.  
2. Un tour más visual e interactivo de la Cloud Console sería útil al inicio.

### Comparación con otros enfoques

- **Vs. videos**: Los labs son más activos y memorables.  
- **Vs. solo documentación**: Proveen una ruta guiada paso a paso.  
- **Vs. proyectos propios desde cero**: Ahorran el esfuerzo de configuración inicial.

---

## Decisiones y Próximos Pasos

### Decisiones Tomadas

1. Usar siempre **navegación privada** para trabajar con labs.  
2. Explorar el menú de servicios de forma sistemática.  
3. Leer las descripciones de APIs antes de habilitarlas.

### Próximos pasos recomendados

1. **Get Started with Cloud Shell and gcloud** para trabajar desde CLI.  
2. Labs de **Compute Engine** (crear máquinas virtuales).  
3. Explorar más APIs en la API Library y relacionarlas con casos de uso reales.  
4. Empezar a mirar contenidos para certificaciones como **Associate Cloud Engineer**.

### Integración con otros temas del curso

- Con **DataOps**: proyectos e IAM son fundamentales para pipelines de datos seguros.  
- Con **MLOps**: muchas APIs de ML siguen el mismo patrón de habilitación y control de acceso.  
- Con **DevOps**: IAM y organización de proyectos son pilares de cualquier pipeline de CI/CD en la nube.

---

## Conclusiones

Este lab introductorio proporcionó una **base sólida para trabajar con Google Cloud Platform**:

- Se entendió cómo se organizan los recursos mediante **proyectos**.  
- Se introdujo el modelo de **seguridad y acceso** a través de **Cloud IAM**.  
- Se vio en la práctica que las **APIs deben habilitarse** antes de ser usadas.  
- Se ganó confianza para navegar la **Cloud Console** y explorar nuevos servicios.

### Valor específico para Ingeniería de Datos

- La separación por proyectos ayuda a aislar ambientes de datos (dev/staging/prod).  
- IAM es clave para controlar quién puede ver, modificar o desplegar pipelines de datos.  
- Las APIs de GCP (BigQuery, Dataflow, Pub/Sub, etc.) se habilitan y gestionan con la misma lógica aprendida aquí.  

Este lab es, en definitiva, el **primer paso necesario** para poder trabajar luego con herramientas más avanzadas de ingeniería de datos en GCP.

---

## Referencias

- [Google Cloud Skills Boost](https://www.skills.google/)  
- [Lab GSP282: A Tour of Google Cloud Hands-on Labs](https://www.skills.google/focuses/2794?parent=catalog)  
- [Google Cloud Console Documentation](https://cloud.google.com/docs/overview)  
- [Cloud IAM Documentation](https://cloud.google.com/iam/docs)  
- [APIs Explorer Directory](https://developers.google.com/apis-explorer)  
