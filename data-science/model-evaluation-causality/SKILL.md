---
name: model-evaluation-causality
description: Guía operativa para evaluación de modelos, diseño experimental (A/B testing) e inferencia causal. Úsala cuando el usuario necesite elegir métricas (accuracy, F1, ROC-AUC, lift), diseñar o analizar un test A/B (CUPED, SRM, poder estadístico), o establecer relaciones causa-efecto con datos observacionales (DoWhy, EconML, DoubleML, DAGs, propensity score). Aplica también ante preguntas sobre validación cruzada temporal, drift en producción (Evidently AI), o "¿cómo sé si mi modelo realmente funciona?".
---

# Evaluación de Modelos, Experimentación e Inferencia Causal

## Cuándo se usa esta skill
Después de entrenar un modelo o tomar una decisión basada en datos: ¿realmente funciona? ¿el efecto es real o es una correlación espuria?

## Evaluación de modelos: qué métrica usar

| Contexto | Métrica | Por qué |
|---|---|---|
| Clasificación balanceada | **Accuracy** | Proporción de aciertos; engañosa si las clases están desbalanceadas |
| Clasificación desbalanceada (fraude, enfermedad) | **Precision + Recall + F1** | Precision = de los que predije positivos, cuántos lo son; Recall = de los reales positivos, cuántos capturé |
| Ranking / priorización de intervenciones | **ROC-AUC, curvas de lift/ganancia** | AUC mide discriminación global; lift muestra cuánto mejora vs. aleatorio en los top-N |
| Regresión | **RMSE, MAE** | RMSE penaliza errores grandes; MAE es robusta a outliers |

**Validación cruzada temporal**: en series de tiempo, nunca uses K-Fold aleatorio — filtra información del futuro. Usa ventana móvil o expansiva secuencial (detalle completo y patrón de código en `ml-supervised-forecasting`).

## Experimentación: A/B testing

**Flujo correcto**: definir hipótesis (H₀, H₁) → calcular tamaño de muestra (Power Analysis, MDE) → aleatorizar → ejecutar → verificar SRM → analizar significancia.

- **CUPED**: reduce la varianza del experimento usando datos pre-experimentales correlacionados con la métrica — permite detectar efectos más pequeños o terminar antes.
- **SRM (Sample Ratio Mismatch)**: si el ratio de usuarios en control/tratamiento difiere del planeado, la aleatorización falló — el experimento no es válido. Verificar siempre con Chi-cuadrado antes de mirar resultados.
- **Plataformas**: **GrowthBook** (open source, warehouse-native, soberanía de datos) vs **Statsig** (SaaS managed, adquirido por OpenAI). GrowthBook si tienes warehouse propio; Statsig si quieres velocidad sin infraestructura.

## Inferencia causal: cuando no puedes aleatorizar

**El flujo DoWhy** (estándar de la industria): Modelar (DAG) → Identificar (backdoor/frontdoor) → Estimar (DML, IPW, Causal Forest) → Refutar (placebo, sensibilidad).

| Método | Cuándo usarlo |
|---|---|
| **Diferencia en Diferencias (DiD)** | Tienes datos pre/post de grupo tratado y control, y puedes defender el supuesto de tendencias paralelas |
| **Regresión Discontinua (RD)** | El tratamiento se asigna por un umbral estricto en una variable continua (ej. beca si nota > X) |
| **Variables Instrumentales (IV)** | Tienes un instrumento que afecta al tratamiento pero no directamente al outcome |
| **Double Machine Learning (DML)** | Alta dimensión de confounders; quieres usar XGBoost/RF para ajustar pero necesitas inferencia causal válida |
| **Causal Forests (EconML)** | Necesitas estimar efectos heterogéneos: ¿a quién le funciona más el tratamiento? |

**Suite PyWhy**: `DoWhy` (estructura y refuta) + `EconML` (estima CATE a escala) + `DoubleML` (inferencia ortogonal rigurosa). DoWhy orquesta; EconML/DoubleML computan.

## Anti-patrones graves

- **Interpretar feature importances de XGBoost como causas**: la importancia mide correlación predictiva, no efecto causal. Si un confounder impulsa tratamiento y outcome a la vez, el modelo le dará alta importancia sin que intervenirlo sirva de nada.
- **Propensity Score Matching (PSM) en alta dimensión**: demostrado matemáticamente que incrementa el sesgo al reducir el espacio a un escalar (King & Nielsen, 2019). Usa Coarsened Exact Matching o DML en su lugar.
- **"Peeking" en A/B tests**: mirar resultados antes de alcanzar el tamaño de muestra requerido infla la tasa de falsos positivos; si necesitas paradas tempranas, usa diseños secuenciales formales.

## Monitoreo post-despliegue: Evidently AI

Cuando el modelo ya está en producción y no tienes etiquetas de verdad inmediatas, Evidently detecta **data drift** (la distribución de las entradas cambió) y **concept drift** (la relación entrada→output cambió). Usa tests estadísticos automáticos (K-S para continuas, Chi² para categóricas, PSI para estabilidad).

*(Esta sección cubre el concepto desde la óptica de evaluación. El flujo operativo completo — integración con Prometheus/Grafana, disparo de reentrenamiento vía Airflow/Prefect, y por qué NO usar Great Expectations para esto — vive en `mlops-production`.)*

## Glosario mínimo
- **ATE/ATT/CATE**: efecto promedio del tratamiento (en toda la población / solo en los tratados / condicional a características X).
- **DAG**: grafo que formaliza tus supuestos causales — sin él, cualquier estimación es una caja negra de supuestos implícitos.
- **Confounder**: variable que causa tanto el tratamiento como el outcome — si no la controlas, la estimación está sesgada.
- **Ortogonalidad de Neyman**: propiedad matemática que hace que DML sea robusto a errores en los modelos auxiliares de predicción.
