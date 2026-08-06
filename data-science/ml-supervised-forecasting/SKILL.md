---
name: ml-supervised-forecasting
description: Guía operativa para machine learning supervisado (regresión/clasificación) y pronóstico de series de tiempo. Úsala cuando el usuario necesite elegir entre scikit-learn, XGBoost, LightGBM, Prophet, statsforecast, sktime, skforecast o mlforecast; diseñar backtesting temporal sin fuga de datos; construir lags/ventanas móviles; o decidir entre modelos clásicos y modelos de fundación para series de tiempo (Chronos, TimesFM). Aplica también ante cualquier mención de ARIMA, SARIMA, validación cruzada temporal, o "mi modelo de series de tiempo funciona en validación pero falla en producción".
---

# Machine Learning Supervisado y Pronóstico de Series de Tiempo

## Cuándo se usa esta skill
Modelado predictivo: variable objetivo etiquetada (continua o categórica), con o sin estructura temporal.

*(Si todavía no limpiaste/exploraste los datos, o no sabes si el proyecto justifica CSDD completo, pasa primero por `data-science-mentor`.)*

## ⚠️ Vigencia de este contenido
Los modelos de fundación para series de tiempo son el área que más rápido cambia de toda la skill. Verifica antes de recomendar como estado del arte:
- Si Chronos-2/TimesFM siguen siendo los TSFMs de referencia o ya salió una generación nueva
- Los resultados de benchmarks citados (GIFT-Eval, fev-bench) contra la versión más reciente publicada

## Regla no negociable: nunca uses K-Fold aleatorio en series de tiempo
`sklearn.model_selection.KFold` en datos temporales filtra información del futuro al pasado (data leakage) — las métricas de validación mienten y el modelo falla en producción. Usa siempre **backtesting temporal** (ventana móvil o expansiva, secuencial).

## Mapa de decisión

| Escenario | Combinación recomendada | Por qué |
|---|---|---|
| Datos caben en RAM, un solo nodo, quieres variables exógenas dinámicas (clima, promociones) | **skforecast + LightGBM** | Estándar de oro local: gestiona lags automáticamente, backtesting riguroso, rápido |
| Millones de series independientes, infraestructura en la nube | **mlforecast + statsforecast + Ray** | statsforecast da baselines univariados ultrarrápidos (Numba, ~20x más rápido que pmdarima); mlforecast escala el ML no lineal; Ray paraleliza |
| Negocio con fuerte estacionalidad humana y festivos, sin trasfondo econométrico | **Prophet** | Modelo aditivo (tendencia + estacionalidad + festivos), pero **no lo uses** en datos de alta frecuencia/ruido (ej. cotizaciones por minuto) — sobreajusta la tendencia y es costoso |
| Investigación multitarea (clasificar + pronosticar bajo un mismo estándar) | **sktime** | Interfaz unificada estilo scikit-learn extendida al plano temporal |
| Necesitas la máxima precisión tabular clásica sin restricción temporal | **XGBoost / LightGBM** | LightGBM más rápido en datasets masivos (histogramas + leaf-wise growth); XGBoost más estable en datasets pequeños/ruidosos |

**Optuna** es el optimizador de hiperparámetros que combina con todo lo anterior (lags óptimos + hiperparámetros del regresor a la vez). **Statsmodels** para diagnóstico previo (prueba de Dickey-Fuller, ACF/PACF) antes de modelar.

## Patrón de código de referencia (skforecast + LightGBM)
```python
from skforecast.recursive import ForecasterRecursive
import lightgbm as lgb

forecaster = ForecasterRecursive(
    regressor=lgb.LGBMRegressor(random_state=42, verbosity=-1),
    lags=14
)
forecaster.fit(y=serie_entrenamiento, exog=variables_exogenas_conocidas)
# Backtesting SIEMPRE antes de confiar en el modelo:
# backtesting_forecaster(forecaster, y=serie, steps=14, refit=True, ...)
```

## Anti-patrones
- **Prophet en datos de alta frecuencia/ruido**: consume recursos sin capturar la dinámica real.
- **Doble escalado sin inversión**: transformar el target (log, diferenciación) fuera del forecaster y *también* configurar escalado interno sin coordinar la reversión — distorsiona las métricas en la escala original.
- **Forecasting recursivo cuando se necesita directo (o viceversa)**: recursivo realimenta sus propias predicciones (acumula error a largo plazo); directo entrena un modelo por paso (más caro, pero no acumula error). Elige según el horizonte y el presupuesto de cómputo.

## Métricas: cuál reportar y por qué
*(Para la filosofía general de selección de métricas — clasificación, ROC-AUC, F1 — la fuente canónica es `model-evaluation-causality`. Aquí solo las específicas de forecasting.)*
- **RMSE**: penaliza fuerte los errores grandes (útil si esos errores son costosos).
- **MAE**: robusta a outliers.
- **MASE**: escala el error contra un pronóstico ingenuo — la más justa para comparar series de distinta magnitud.
- **WQL (Weighted Quantile Loss)**: estándar para evaluar pronósticos probabilísticos (cuantiles), no solo el valor puntual.

## Tendencia 2024-2026 a tener en cuenta
Los **modelos de fundación para series de tiempo (TSFMs)** — Chronos-2 (Amazon), TimesFM (Google) — hacen inferencia *zero-shot* sin reentrenar. Son útiles como referencia rápida o en ensamble, pero **evalúalos siempre contra un baseline local (AutoARIMA de statsforecast)** usando benchmarks sin fuga de datos (GIFT-Eval, fev-bench) antes de justificar su costo computacional frente a un LightGBM bien afinado.
