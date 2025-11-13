---
title: "03 - 🚖 Conectando la ciudad: análisis multifuente de taxis en Nueva York"
date: 2025-08-27
---

# 🚖 Conectando la ciudad: análisis multifuente de taxis en Nueva York

## Contexto
En esta práctica trabajamos con datos de taxis de Nueva York y fuentes adicionales, con el objetivo de realizar un análisis exploratorio integrando múltiples datasets. La tarea principal fue practicar operaciones de **join** para combinar información complementaria y generar nuevos insights.

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

- [Notebook completo](../recursos_files/tres_taxis_m.ipynb)

### Como llegamos a los resultados y un análisis correspondiente de estos

#### ***CARGAR DATOS DE MÚLTIPLES FUENTES***

**Cómo se hizo:**  
Se trabajó con tres datasets oficiales y un calendario de eventos:  
- **Viajes de taxis (Parquet):** se cargaron más de 3 millones de registros del dataset oficial de enero 2023 en formato `.parquet`, lo que permitió un manejo más eficiente que con CSV.  
- **Zonas (CSV):** se incorporó el archivo oficial de zonas de NYC con 265 registros, incluyendo `LocationID`, `Borough`, `Zone` y `service_zone`.  
- **Calendario (JSON):** se integró un calendario de eventos locales, convirtiendo la columna de fechas de string a tipo fecha.  

Se verificó la cantidad de filas, columnas, tipos de datos y un rango temporal de los viajes. Además, se revisaron las primeras filas de cada dataset para confirmar la correcta lectura e integración.

#### ***NORMALIZAR Y PREPARAR DATOS PARA JOINS***

**Cómo se hizo:**  
- **Estandarización de columnas:** se pasaron todos los nombres a minúsculas en `trips` y `zones` para evitar errores por mayúsculas/minúsculas en los merges.  
- **Columna de fecha:** a partir de `tpep_pickup_datetime` se creó `pickup_date` (solo fecha) para poder unir con el calendario de eventos.  
- **Verificación de tipos:** se chequearon los dtypes de las claves (`pulocationid` ↔ `locationid`, `pickup_date` ↔ `calendar.date`) para asegurar compatibilidad.  
- **Optimización de memoria:** se rellenaron nulos críticos y se ajustaron tipos numéricos (`pulocationid`, `dolocationid` a `int16`; `passenger_count` a `int8`; `locationid` a `int16`). Esto redujo el uso de memoria del DataFrame de viajes.  
- **Control de nulos:** se listaron las columnas con más faltantes y se definieron acciones:  
  - En `trips`: se eliminaron filas con valores nulos (criterio conservador dada la escala del dataset).  
  - En `zones`: se imputaron nulos con `'Desconocido'` en `borough`, `zone` y `service_zone`.  
- **Calidad de datos:** se confirmaron conteos totales y que las claves de ubicación necesarias para los joins quedaran completas tras la limpieza.

Resultado: `trips` y `zones` quedan **normalizados, tipados y sin nulos críticos**, listos para realizar los **joins** con zonas y calendario.

#### ***PRIMER JOIN: TRIPS + ZONES***

**Cómo se hizo:**  
Se unió la tabla de viajes con la tabla de zonas usando `merge` de pandas con **left join**:
- Clave de unión: `trips.pulocationid` ↔ `zones.locationid`.  
- Se mantuvieron **todos** los registros de `trips` y se anexaron las columnas geográficas (`borough`, `zone`, `service_zone`).  
- Se verificó la integridad: mismo número de filas antes y después del join, y conteo de nulos en `borough` para detectar IDs sin correspondencia.

**Resultado:**  
- Filas antes/después: **2,995,023 → 2,995,023** (no se perdieron viajes).  
- Columnas añadidas: `locationid`, `borough`, `zone`, `service_zone`.  
- **Nulos en `borough`: 0** (join exitoso).  
- Distribución de pickups por borough (top): **Manhattan ~2.65M**, **Queens ~285K**, **Unknown ~39K**, **Brooklyn ~15K**, **Bronx ~3.9K**.

#### ***SEGUNDO JOIN: TRIPS_ZONES + CALENDAR***

**Cómo se hizo:**  
- Se realizó un **left join** entre el dataset de viajes con zonas (`trips_with_zones`) y el **calendario** usando como clave la fecha de pickup: `pickup_date` ↔ `date`.  
- Se **conservaron todos los viajes** aunque no existiera evento en el calendario (por eso el `how='left'`).  
- Se creó la bandera **`is_special_day`** rellenando los nulos de `special` con `False` para tener un campo booleano uniforme.  
- Se verificó la **integridad** del resultado: mismo número de filas antes y después del join y **sin nulos** en las columnas clave utilizadas en el análisis posterior (`borough`, `zone`, `trip_distance`, `total_amount`, `is_special_day`).

#### ***ANÁLISIS AGREGADO POR BOROUGH***

**Cómo se hizo:**  
- Se agruparon los viajes por `borough` usando `groupby` y se calcularon estadísticas descriptivas: número de viajes, distancia media, mediana y desviación estándar, totales de tarifa y propinas, y promedio de pasajeros.  
- Se aplanaron las columnas de múltiples niveles para facilitar la lectura.  
- Se añadieron métricas adicionales de negocio:  
  - **`revenue_per_km`** = ingreso promedio por kilómetro.  
  - **`tip_rate`** = porcentaje de propina respecto a la tarifa base.  
  - **`market_share`** = participación de cada borough en el total de viajes.  
- Finalmente, se ordenaron los resultados por número de viajes y se extrajeron algunos insights clave.

```
Análisis por Borough (procesando datos grandes)...

ANÁLISIS COMPLETO POR BOROUGH:
               num_trips  avg_distance  std_distance  median_distance  \
borough                                                                 
Manhattan        2648320          2.41         40.73             1.61   
Queens            285126         12.32         12.31            11.27   
Unknown            39788          7.59        145.56             2.63   
Brooklyn           15478          4.48          4.94             3.03   
Bronx               3931          5.19          6.40             2.90   
Desconocido         1632          2.34          7.56             0.00   
EWR                  409          1.60          5.68             0.00   
Staten Island        339         11.34         10.24            14.80   

               avg_total  std_total  median_total  avg_fare  avg_tip  \
borough                                                                
Manhattan          22.35      14.47         19.00     14.64     2.86   
Queens             67.35      33.65         70.45     50.04     7.85   
Unknown            38.12      30.48         25.30     26.47     4.82   
Brooklyn           32.02      23.17         27.00     26.20     2.62   
Bronx              34.15      33.83         29.00     30.01     0.65   
Desconocido       108.20      96.90         91.20     95.08     9.93   
EWR               104.34      62.82        118.50     87.99    12.43   
Staten Island      62.44      45.04         67.80     48.68     1.29   

               median_tip  avg_passengers  
borough                                    
Manhattan            2.66            1.36  
Queens               8.18            1.39  
Unknown              3.14            1.35  
Brooklyn             0.00            1.26  
Bronx                0.00            1.10  
Desconocido          0.01            1.43  
EWR                 10.00            1.58  
Staten Island        0.00            1.13  

ANÁLISIS CON MÉTRICAS EMPRESARIALES:
               num_trips  market_share  revenue_per_km  tip_rate
borough                                                         
Manhattan        2648320          88.4            9.27      19.5
Queens            285126           9.5            5.47      15.7
Unknown            39788           1.3            5.02      18.2
Brooklyn           15478           0.5            7.15      10.0
Bronx               3931           0.1            6.58       2.2
Desconocido         1632           0.1           46.24      10.4
EWR                  409           0.0           65.21      14.1
Staten Island        339           0.0            5.51       2.6

INSIGHTS PRINCIPALES:
   Borough con más viajes: Manhattan
   Borough con viajes más largos: Queens
   Borough con tarifas más altas: ['Desconocido', 'EWR', 'Queens']
   Mejor revenue por km: EWR
```

**Qué muestran los resultados:**  
- **Manhattan** concentra la gran mayoría de los viajes (**88% del total**), aunque con distancias relativamente cortas (media de 2.4 km).  
- **Queens** tiene viajes mucho más largos en promedio (12 km), lo que eleva las tarifas y la mediana de pago.  
- Los boroughs etiquetados como **Desconocido** y **EWR (aeropuerto Newark)** muestran tarifas y `revenue_per_km` muy altos, probablemente por tratarse de trayectos especiales o zonas con recargos.  
- En propinas, la tasa más alta promedio se observa en Manhattan (19.5%), seguida de Unknown (18.2%) y Queens (15.7%). En contraste, Bronx y Staten Island presentan propinas mucho más bajas.  
- Los insights finales confirman que **Manhattan domina en volumen**, **Queens en longitud de viajes**, y **EWR en ingreso por kilómetro**.

#### ***ANÁLISIS COMPARATIVO: DÍAS NORMALES VS ESPECIALES***

**Cómo se hizo:**  
- Se agruparon los viajes por `borough` y por la variable `is_special_day` para comparar el comportamiento en días normales y días especiales.  
- Se calcularon métricas agregadas: número de viajes, distancia promedio y tarifa promedio.  
- También se generó un resumen general comparando únicamente los dos tipos de días, y se verificó si existían datos suficientes para observar diferencias.  

```
   vendorid tpep_pickup_datetime tpep_dropoff_datetime  passenger_count  \
0         2  2023-01-01 00:32:10   2023-01-01 00:40:36                1   
1         2  2023-01-01 00:55:08   2023-01-01 01:01:27                1   
2         2  2023-01-01 00:25:04   2023-01-01 00:37:49                1   
3         1  2023-01-01 00:03:48   2023-01-01 00:13:25                0   
4         2  2023-01-01 00:10:29   2023-01-01 00:21:19                1   

   trip_distance  ratecodeid store_and_fwd_flag  pulocationid  dolocationid  \
0           0.97         1.0                  N           161           141   
1           1.10         1.0                  N            43           237   
2           2.51         1.0                  N            48           238   
3           1.90         1.0                  N           138             7   
4           1.43         1.0                  N           107            79   

   payment_type  ...  airport_fee  pickup_date  locationid    borough  \
0             2  ...         0.00   2023-01-01         161  Manhattan   
1             1  ...         0.00   2023-01-01          43  Manhattan   
2             1  ...         0.00   2023-01-01          48  Manhattan   
3             1  ...         1.25   2023-01-01         138     Queens   
4             1  ...         0.00   2023-01-01         107  Manhattan   

                zone  service_zone  date  name  special is_special_day  
0     Midtown Center   Yellow Zone   NaN   NaN      NaN          False  
1       Central Park   Yellow Zone   NaN   NaN      NaN          False  
2       Clinton East   Yellow Zone   NaN   NaN      NaN          False  
3  LaGuardia Airport      Airports   NaN   NaN      NaN          False  
4           Gramercy   Yellow Zone   NaN   NaN      NaN          False  

[5 rows x 28 columns]
Análisis: Borough + Día Especial...

ANÁLISIS BOROUGH + DÍA ESPECIAL:
                              num_trips  avg_distance  avg_total
borough       is_special_day                                    
Bronx         False                3931          5.19      34.15
Brooklyn      False               15478          4.48      32.02
Desconocido   False                1632          2.34     108.20
EWR           False                 409          1.60     104.34
Manhattan     False             2648320          2.41      22.35
Queens        False              285126         12.32      67.35
Staten Island False                 339         11.34      62.44
Unknown       False               39788          7.59      38.12

COMPARACIÓN DÍAS NORMALES VS ESPECIALES:
            Avg Distance  Avg Amount  Num Trips
Día Normal          3.44       26.97    2995023

SOLO HAY Día Normal:
   Viajes: 2,995,023.0
   Distancia promedio: 3.44 millas
   Tarifa promedio: $26.97
   No hay datos de días especiales para comparar en este período
   ```

**Qué muestran los resultados:**  
En el período analizado solo hubo registros correspondientes a **días normales**.  
- Se realizaron alrededor de **2,99 millones de viajes**, con una distancia promedio de **3.44 millas** y una tarifa promedio de **26.97 USD**.  
- No hubo eventos catalogados como “días especiales” en el calendario para este rango de fechas, por lo cual no se pudo establecer una comparación directa.  

Esto implica que el análisis de impacto de eventos especiales sobre el uso de taxis queda pendiente para meses donde el calendario incluya feriados o días con promociones/eventos relevantes.

#### ***TÉCNICAS PARA TRABAJAR CON DATASETS GRANDES***

**Cómo se hizo:**  
- **Sampling estratégico:** dado que el dataset superaba los 2,9 millones de registros, se creó una muestra aleatoria de **10.000 viajes** (0,3% del total) para facilitar futuras visualizaciones y pruebas sin comprometer la representatividad.  
- **Análisis de performance de joins:** se calcularon métricas de calidad de integración: número total de viajes, porcentaje de coincidencia con zonas, cantidad de zonas únicas utilizadas y cobertura respecto al total de zonas disponibles.  
- **Análisis temporal avanzado:** se extrajo la hora de `tpep_pickup_datetime` para estudiar patrones de viajes por hora, incluyendo cantidad de viajes, tarifa promedio y distancia promedio.

```
⚡ Aplicando técnicas para datasets grandes...
Dataset grande detectado: 2,995,023 registros
Creando muestra estratificada para visualizaciones...
Muestra creada: 10,000 registros (0.3%)

ANÁLISIS DE PERFORMANCE:
   total_trips: 2,995,023
   matched_zones: 2,995,023
   match_rate: 100.0%
   unique_zones_used: 255
   total_zones_available: 265
   zone_coverage: 96.2%

ANÁLISIS TEMPORAL AVANZADO:
Horas pico por número de viajes:
      18:00 - 210,761.0 viajes
      17:00 - 204,808.0 viajes
      15:00 - 193,114.0 viajes
```

**Qué muestran los resultados:**  
- El join con zonas fue **100% exitoso**, asignando un borough a todos los viajes.  
- Se usaron **255 zonas distintas de 265 posibles**, lo que da una **cobertura del 96,2%** del dataset.  
- En el análisis horario, se identificaron **picos de viajes en la tarde**, especialmente a las **18:00 (210.761 viajes)**, seguidos de las **17:00 y 15:00 horas**, lo cual refleja los horarios de mayor demanda en la ciudad.

#### ***ANÁLISIS DE CORRELACIONES NUMÉRICAS (RESULTADOS)***

**Cómo se hizo:**  
- Se seleccionaron las variables numéricas clave: `trip_distance`, `total_amount`, `fare_amount` y `tip_amount`.  
- Se calculó la **matriz de correlación** con `.corr()`, que mide la fuerza y dirección de la relación lineal entre pares de variables.  
- Luego, se ordenaron las correlaciones más fuertes para destacar los vínculos más relevantes.

```
Matriz de Correlación:
               trip_distance  total_amount  fare_amount  tip_amount
trip_distance          1.000         0.094        0.094       0.061
total_amount           0.094         1.000        0.980       0.708
fare_amount            0.094         0.980        1.000       0.588
tip_amount             0.061         0.708        0.588       1.000

Correlaciones más fuertes:
   total_amount vs fare_amount: 0.980
   total_amount vs tip_amount: 0.708
   fare_amount vs tip_amount: 0.588

INTERPRETACIÓN DE CORRELACIONES:
   > 0.7: Correlación fuerte positiva
   0.3-0.7: Correlación moderada positiva
   -0.3-0.3: Correlación débil
   < -0.7: Correlación fuerte negativa
```
**Qué muestra la tabla de correlaciones:**  
- Existe una **correlación muy fuerte (0.98)** entre `total_amount` y `fare_amount`, lo cual es lógico ya que la tarifa base compone gran parte del total.  
- La relación entre `total_amount` y `tip_amount` es **fuerte y positiva (0.71)**, lo que indica que a mayor costo del viaje, mayores propinas en promedio.  
- La correlación entre `fare_amount` y `tip_amount` es **moderada (0.59)**, reforzando la misma idea.  
- En cambio, la **distancia (`trip_distance`) muestra correlaciones muy bajas** con las demás variables (< 0.1), lo que sugiere que la tarifa no depende linealmente de la distancia, sino que influyen otros factores como recargos, peajes o congestión.

Entonces la matriz confirma que las variables económicas (`fare_amount`, `total_amount`, `tip_amount`) están fuertemente relacionadas entre sí, mientras que la distancia recorrida no es un predictor directo de los costos finales.

## Reflexión

### Preguntas (y respuestas) finales

1* ¿Qué diferencia hay entre un LEFT JOIN y un INNER JOIN?

Usando el left join te quedas con los datos de la tabla, es la que se mantiene, mientras que de la derecha son los valores que se agregan. Usando inner join se hace interseccion de las dos tablas.

2* ¿Por qué usamos LEFT JOIN en lugar de INNER JOIN para trips+zones?

Porque al hacer left te aseguras que vas a mantener toda la informacion de los viajes agregando las zonas correspondientes a los mismos. Si hicieramos inner vamos a perder la informacion de los trips que no tienen zona asignada.

3* ¿Qué problemas pueden surgir al hacer joins con datos de fechas?

Diferencias en el tipo de dato, por ejemplo: string o datetime. • Formatos de fecha distintos, por ejemplo: YYYY-MM-DD o DD/MM/YYYY. • Valores nulos o fechas faltantes que pueden impedir el join.

4* ¿Cuál es la ventaja de integrar múltiples fuentes de datos?

Nos permite realizar un análisis más completo y contextualizado. Además, se pueden cruzar variables de diferentes bases para descubrir patrones que no serían visibles en un solo dataset; esto enriquece la información y habilita conclusiones más profundos.

5* ¿Qué insights de negocio obtuviste del análisis integrado?

Manhattan concentra la mayoría de los viajes, los viajes en Queens son más largos y costosos en promedio y el mejor revenue por km son de EWR. Hay diferencias claras en el revenue por kilómetro y en la tasa de propinas entre boroughs y los días especiales pueden tener impacto en la distancia y tarifa promedio.

### Preguntas (y respuestas) bonus
(no pudimos hacer funcionar el código pero buscamos información para poder contestar a estas preguntas)

1* ¿Qué ventaja tiene usar @task en lugar de una función normal?

Una función normal en Python se ejecuta sin ningún control extra.

Cuando usas @task:

- Podés definir reintentos automáticos si falla por algo temporal (ej: red caída al bajar datos).

- Podés definir cortes por tiempo (si un paso tarda demasiado, Prefect lo corta).

- Tenés logs integrados en la interfaz de Prefect.

- Cada task queda orquestado y monitoreado: podés ver qué falló y reintentar sólo ese paso.


2* ¿Para qué sirve el @flow decorator?

Le dice a Prefect: “Esto no es sólo un script de Python, es un flujo de trabajo con dependencias y monitoreo”.

Permite:

- Ejecutar tasks en orden y pasar resultados de uno a otro.

- Monitorear el flow run entero.


3* ¿En qué casos reales usarías esto?

Reportes diarios: Automatizar que cada mañana se bajen datos de ventas, se limpien y se envíen reportes a un dashboard.

Análisis automáticos: Procesar logs de usuarios, detectar anomalías o generar alertas de fraude sin intervención manual.

Pipelines de ML: cargar dataset - limpiar/preprocesar - entrenar modelo - guardar métricas

### Reflexión final

Esta práctica me permitió reforzar la importancia de trabajar con múltiples fuentes de datos y cómo los **joins** son fundamentales para enriquecer un análisis.  
Lo más desafiante fue asegurar que las claves de unión coincidieran y manejar los datos faltantes.  
Lo más valioso fue comprobar cómo la integración de datasets permite descubrir patrones y relaciones que no serían visibles analizando cada fuente por separado.  
Un próximo paso sería profundizar en métricas descriptivas y aplicar visualizaciones en prácticas futuras para interpretar mejor la información integrada.
Aunque no pudimos realizar la parte práctica del bonus, considero que fue interesante ya entrar en prefect y las herramientas que nos ofrece.

## Referencias
- Dataset: NYC Taxis  
- Documentación de pandas (merge, join, concat)
