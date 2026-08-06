---
name: eda-feature-engineering
description: Guía operativa para análisis exploratorio de datos (EDA), visualización diagnóstica y feature engineering. Úsala cuando el usuario necesite perfilar un dataset, elegir entre ydata-profiling/sweetviz/dtale, diagnosticar patrones de nulos, elegir el coeficiente de correlación correcto (Pearson/Spearman/Phik/Cramér's V), imputar valores, codificar variables categóricas, discretizar, crear variables nuevas, seleccionar features, o construir features desde tablas relacionales con featuretools. Aplica también si se menciona "data leakage" en preprocesamiento.
---

# Análisis Exploratorio de Datos (EDA), Visualización y Feature Engineering

## Cuándo se usa esta skill
Fase entre la limpieza de datos y el modelado: explorar la estructura del dataset, diagnosticar patologías estadísticas y transformar variables crudas en features aptas para un modelo.

## AutoEDA: qué herramienta recomendar

| Herramienta | Cuándo usarla | Nivel |
|---|---|---|
| **`programmatic-eda`** (skill propia) | Ya tienes scripts ejecutables (`null_profiler.py`, `outlier_detector.py`, `correlation_explorer.py`) con checklist de 40 ítems y plantilla de reporte — úsala primero, sin dependencias nuevas. Nota: su script de correlación por defecto es Pearson-solo-numérico y su z-score es clásico — complementa con la guía de coeficientes y outliers de esta skill antes de confiar en esos defaults a ciegas. | Entrada, siempre que aplique |
| **ydata-profiling** (antes pandas-profiling) | Reporte completo en una línea (`.profile_report()`): overview, correlaciones (Pearson/Spearman/Kendall/Phik/Cramér's V), nulos, muestra. Soporta Spark/PySpark para escala. | Cuando se necesita el reporte HTML compartible que `programmatic-eda` no genera |
| **sweetviz** | Comparar dos particiones (train vs test) o analizar correlación de predictores contra el target (`sv.compare()`) | Entrada, análisis de target |
| **dtale** | Servidor interactivo (Flask+React) sobre el DataFrame: editar celdas, filtrar con queries, calcular Predictive Power Score (PPS), exportar el código Python de cada operación | Avanzado, exploración manual profunda |

## Visualización exploratoria: qué librería usar

Esto es distinto de comunicar un hallazgo ya validado — para eso (audiencia ejecutiva, dashboards) la fuente es `data-storytelling-bi`. Aquí es exploración propia, sin audiencia externa todavía.

| Si necesitas... | Usa | Nota |
|---|---|---|
| Gráficos rápidos y de control total sobre cada detalle (papers, figuras exactas) | **Matplotlib** | Verboso pero sin límites; base sobre la que corren seaborn y pandas.plot |
| Gráficos estadísticos con defaults atractivos en pocas líneas (distribuciones, correlaciones, categóricas) | **Seaborn** | `pairplot`, `heatmap`, `violinplot` — ideal para el EDA del día a día, construido sobre Matplotlib |
| Interactividad (zoom, hover, filtrar) sin montar un dashboard completo | **Plotly** (`plotly.express`) | Una línea por gráfico interactivo; exporta a HTML standalone para compartir sin servidor |
| Miles/millones de puntos sin que el navegador se congele | **Datashader** o **Bokeh** con muestreo | Matplotlib/Seaborn/Plotly renderizan cada punto — a partir de ~100K puntos hay que agregar o usar rasterización |

**Anti-patrón**: usar Plotly por defecto para todo el EDA porque "se ve mejor" — el overhead de renderizado interactivo en notebooks grandes con muchas celdas ralentiza la exploración. Reserva Plotly para cuando la interactividad aporta valor real (explorar un scatter con miles de categorías), y usa Seaborn/Matplotlib para el resto.

## Diagnóstico visual de nulos (en este orden)
1. **Count plot**: completitud por columna, aislada.
2. **Matrix plot**: si la ausencia sigue un patrón secuencial (ej. caída de un sensor) vs. aleatorio.
3. **Dendrograma de nulidad**: agrupa variables que se ausentan juntas → sugiere dependencia lógica entre ellas.

## Qué coeficiente de correlación usar
- **Pearson (r)**: dos variables continuas, relación lineal.
- **Spearman (ρ) / Kendall (τ)**: relación monótona no lineal, distribuciones sesgadas (usa el rango, no el valor).
- **Phik (φk)**: relación no lineal entre variables mixtas (categóricas + numéricas) — el más generalizable.
- **Cramér's V**: solo entre variables categóricas (o numérica discretizada vs categórica).

No uses Pearson por defecto para todo — es el error más común en un EDA apurado.

## Feature Engineering con `feature-engine` (compatible con pandas, preserva nombres de columnas — a diferencia de scikit-learn puro)

Patrón `.fit()` / `.transform()` **siempre**: `.fit()` solo en train, `.transform()` en el resto. No calcular estadísticas globales antes del split → eso es fuga de datos.

**Imputación**: `MeanMedianImputer` · `ArbitraryNumberImputer` · `RandomSampleImputer` (preserva la distribución original) · `EndTailImputer` (asume que el nulo es un extremo) · `CategoricalImputer` · `AddMissingIndicator` (crea el flag binario de "esto era nulo" — úsalo casi siempre junto con la imputación numérica, no en su lugar).

**Codificación categórica**: `OneHotEncoder`/`OrdinalEncoder` (básicas) · `CountFrequencyEncoder` · `MeanEncoder`/Target Encoding (requiere regularización, riesgo de overfitting) · `WoEEncoder` (estándar en riesgo crediticio) · `RareLabelEncoder` (agrupa categorías poco frecuentes — previene overfitting en alta cardinalidad) · `DecisionTreeEncoder` · `StringSimilarityEncoder` (para texto con errores de tipeo).

**Outliers**: `Winsorizer` (trunca por percentil/std) · `ArbitraryOutlierCapper` · `OutlierTrimmer` (elimina filas — usar con cautela, reduce el dataset).

**Discretización**: `EqualFrequencyDiscretiser` · `EqualWidthDiscretiser` · `DecisionTreeDiscretiser` (cortes óptimos supervisados por el target).

**Transformación de distribución**: Box-Cox (solo valores positivos) · Yeo-Johnson (admite negativos/ceros — preferible por defecto si hay dudas sobre el signo).

**Creación de variables**: `MathFeatures` (combinaciones aritméticas) · `RelativeFeatures` (razones/diferencias respecto a una referencia) · `CyclicalFeatures` (seno/coseno para variables cíclicas como mes o día de semana — así diciembre queda "cerca" de enero) · para series de tiempo: `LagFeatures`, `WindowFeatures`, `ExpandingWindowFeatures`.

## Pipeline de selección de variables (orden recomendado)
```
DropConstantFeatures → DropDuplicateFeatures → DropCorrelatedFeatures
→ SmartCorrelatedSelection → SelectByShuffling
```
`SmartCorrelatedSelection` es superior a `DropCorrelatedFeatures` simple: en cada grupo colineal decide cuál variable conservar según menor nulidad, mayor varianza o mejor desempeño predictivo univariado — no se queda con "la primera que aparece".

## Feature engineering relacional: `featuretools` (Deep Feature Synthesis)
Para datos repartidos en varias tablas (transacciones, clientes, pedidos) unidas por llaves. Construye un `EntitySet` con las relaciones uno-a-muchos, y aplica primitivas de **agregación** (SUM, MEAN, STD, MODE, NUM_UNIQUE sobre la tabla hija) y de **transformación** (dentro de la misma tabla), de forma recursiva.

**Regla de oro anti-fuga temporal**: usar `cutoff times` — el algoritmo solo debe ver transacciones anteriores al momento en que se necesita predecir. Flujo correcto en producción: (1) construir el `EntitySet` y correr DFS **solo con train**, guardando las definiciones de features; (2) para test/inferencia, crear un `EntitySet` nuevo y recalcular con `ft.calculate_feature_matrix` usando esas mismas definiciones guardadas — nunca mezclar ambos conjuntos en un solo DFS.

## Anti-patrón crítico transversal
Ajustar (`.fit()`) cualquier imputador, escalador o encoder sobre el dataset completo antes de separar train/test. Es la causa número uno de métricas de validación infladas que colapsan en producción.
