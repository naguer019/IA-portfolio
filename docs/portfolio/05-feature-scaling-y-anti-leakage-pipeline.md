---
title: "05 Feature scaling y anti leakage pipeline (Ames Housing)"
date: 2025-09-21
---

# Escalado, Outliers y Pipelines (Ames Housing)

## Contexto
Práctica de Ingeniería de Datos/ML enfocada en **preprocesamiento numérico**: detección de outliers, efecto del **escalado de features**, elección de transformadores y **prevención de data leakage** al construir pipelines reproducibles. Se trabajó con el dataset **Ames Housing** (2930 filas, 82 columnas). Se introdujeron faltantes sintéticos para ejercitar imputación y análisis de sesgo.

## Objetivos
- Identificar cuáles features del dataset Ames **necesitan escalado** y por qué.  
- Experimentar con **MinMaxScaler, StandardScaler y RobustScaler** en datos reales.  
- Descubrir el **impacto del escalado** en diferentes algoritmos mediante experimentación.  
- Comparar **pipelines con y sin data leakage** para ver las diferencias.  
- Decidir **la mejor estrategia** basándose en evidencia empírica.

## Actividades (con tiempos estimados)
- Preparación de entorno y dataset — **25 min**  
- Exploración inicial (escalas, outliers, ratios) — **55 min**  
- Experimentos con escalers clásicos (Standard/MinMax/Robust) — **45 min**  
- Trabajo independiente con transformador avanzado (**PowerTransformer – Yeo-Johnson**) — **45 min**  
- Demostración anti-leakage (método incorrecto, correcto y **Pipeline**) — **40 min**  
- Validación con **cross-validation** y baseline — **35 min**  
- Redacción de evidencias y conclusiones — **40 min**  
- Subida y formateo para el portfolio — **40 min**

---

## Desarrollo
1. **Exploración y escalas.** Se calcularon rangos y ratios *max/min* por columna y se graficaron **boxplots en log1p** e **histogramas**. Emergieron como variables problemáticas: `Lot Area`, `Misc Val`, `Total Bsmt SF` y `SalePrice` (colas largas y fuerte asimetría).  
2. **Outliers.** Se aplicaron **IQR** y **Z-Score** (umbral=3) a los datos originales y luego a los datos escalados con tres scalers.  
3. **Transformador avanzado.** Se evaluó **PowerTransformer (Yeo-Johnson)** para corregir asimetría (no lineal, aprende λ por máxima verosimilitud).  
4. **Leakage.** Se compararon tres flujos:  
   - ❌ Escalar todo y después *train/test split* (con leakage).  
   - ✅ Split primero y escalar **solo con train** (sin leakage).  
   - ✅ `sklearn.Pipeline` (anti-leakage automático).  
5. **Validación.** Se montó un pipeline (**PowerTransformer + KNN**) y se evaluó con **CV=5** y baseline (`DummyRegressor`).

---

## Evidencias



## Conclusiones
- **PowerTransformer (Yeo-Johnson)** fue el mejor para variables con **sesgo y colas largas** (Ames las tiene).  
- El **orden importa**: analizar/mitigar outliers **antes** de escalar.  
- **Scalers lineales** (Standard/MinMax/Robust) no cambian la forma; Yeo-Johnson sí.  
- **Pipeline + CV** evita leakage y da evaluación honesta.  
- Pipeline final (**Yeo-Johnson + KNN**) > baseline en R² y MAE.

---

## Recomendación final (pipeline)
1. **Detección/tratamiento de outliers** en crudo (IQR/Z + criterio de dominio).  
2. **Train/Test split** al inicio.  
3. **PowerTransformer (Yeo-Johnson)** para features sesgadas; StandardScaler para el resto.  
4. **Modelo sensible a escala** (p.ej., KNN/SVM) dentro de **`Pipeline`**.  
5. **Cross-validation** y baseline para contrastar.

---

## Referencias
- Dataset: **Ames Housing** (Kaggle).  
- `scikit-learn` — `StandardScaler`, `MinMaxScaler`, `RobustScaler`, `PowerTransformer`, `Pipeline`, `cross_val_score`.  
- Apuntes de la práctica 05 (outliers) y práctica 06 (escalado/leakage).
