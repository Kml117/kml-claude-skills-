---
name: data-cleaning-quality
description: Guía operativa para limpieza, preparación y control de calidad de datos (data wrangling). Úsala siempre que el usuario necesite tratar datos faltantes, outliers, duplicados, validar esquemas, escribir "expectations"/tests de calidad, diseñar contratos de datos, o elegir entre Great Expectations, dbt-expectations, Soda Core, Pandera o Deequ/PyDeequ. Aplica también cuando se mencionen términos como MCAR/MAR/MNAR, data contract, data observability, o "por qué mi pipeline sigue recibiendo datos sucios".
---

# Limpieza, Preparación y Calidad de Datos

## Cuándo se usa esta skill
Se activa en la fase del pipeline que va **después de la ingesta/ETL y antes del EDA o el modelado**: cuando hay que decidir cómo tratar nulos, outliers, duplicados, inconsistencias de tipo, o cómo blindar un pipeline para que no reciba (o no propague) datos corruptos.

## Mapa de decisión: qué framework usar

| Si el usuario... | Usa | Por qué |
|---|---|---|
| Trabaja en pandas/Polars, en memoria, quiere tipado de DataFrame y validaciones al vuelo (ideal para feature engineering) | **Pandera** | `DataFrameSchema` / `DataFrameModel` estilo Pydantic; soporta Pandas, Polars (lazy vía Narwhals), PySpark, Dask |
| Ya usa **dbt** como motor de transformación | **dbt-expectations** | Compila las validaciones a SQL nativo (pushdown), no saca datos del warehouse |
| Necesita contratos de datos legibles por no-ingenieros, en arquitecturas de malla de datos (data mesh) | **Soda Core (SodaCL)** | DSL en YAML simplificado, corre en el warehouse sin extraer datos |
| Necesita documentación automática y visual (Data Docs) para auditores/negocio | **Great Expectations (GX Core)** | Autodocumentación HTML, Checkpoints, Expectation Suites |
| Trabaja con **Spark / lagos de datos a escala de petabytes** | **Deequ / PyDeequ** | Traduce aserciones a jobs de Spark; infiere reglas automáticamente |

**No uses** un motor externo pesado (GX) si dbt ya cubre el caso con tests genéricos — es sobrecarga innecesaria. **No uses** Pandera sobre Spark forzando conversión a pandas local: revienta la memoria del nodo driver.

## Combinaciones ganadoras (úsalas como plantilla de arquitectura)

1. **"Modern Analytics QA Suite"** — `dbt-expectations` (reglas estáticas en el warehouse) + `Elementary Data` (observabilidad pasiva: detecta anomalías de volumen/frescura que ningún test cubrió). Estándar en equipos que ya viven en Snowflake/BigQuery.
   ```yaml
   columns:
     - name: sale_amount
       tests:
         - dbt_expectations.expect_column_values_to_be_between:
             min_value: 0
             max_value: 1000000
   ```
2. **"Enterprise Streaming DQ Gate"** — `PyDeequ` sobre AWS Glue/S3, resultados catalogados en Athena. Para flujos masivos e incrementales.
3. **"Scientific Edge Preprocessing"** — `Pandera` + `Polars` (backend Narwhals, validación *lazy* sin forzar `collect()`). Para microservicios de inferencia y feature engineering local.

## El flujo mental para cualquier caso de limpieza

1. **Diagnostica el mecanismo de ausencia antes de imputar**: MCAR (al azar puro) / MAR (correlacionado con otra variable observada) / MNAR (correlacionado con el propio valor ausente). El mecanismo determina el método válido — imputar con la media un MNAR sesga el análisis silenciosamente.
2. **Outliers**: usa Z-score modificado (mediana + MAD) en vez de Z-score clásico si la distribución es asimétrica — es robusto a los propios extremos que buscas detectar.
3. **Antes de escribir ninguna regla, pregúntate**: ¿esto debería bloquear el pipeline (compuerta de calidad / CI-CD) o solo alertar (observabilidad)? Esa decisión define si va en un contrato de datos versionado en Git o en una herramienta de monitoreo pasivo.

## Anti-patrones a evitar activamente
- **Validación redundante**: la misma regla de unicidad/no-nulidad en GX *y* en dbt sobre la misma tabla — duplica cómputo y genera alertas duplicadas en canales distintos.
- **Imputación estática ciega**: rellenar con media/mediana sin antes chequear el mecanismo de ausencia y sin usar `AddMissingIndicator` para preservar la señal de "esto estaba vacío".

## Glosario mínimo operativo
- **Data Contract**: especificación de esquema + SLA como código (YAML versionado en Git), validado como *quality gate* en CI/CD — no en producción ya consolidada. Es el paradigma dominante 2024-2026, reemplazando la validación puramente reactiva.
- **Fuga de datos (Data Leakage)**: calcular estadísticas (media, escaladores) sobre todo el dataset antes de separar train/test. Siempre `.fit()` solo en train, `.transform()` en el resto.
- **Data Observability**: monitoreo continuo de volumen, frescura y linaje — complementa, no reemplaza, los tests declarativos.

## Cuándo profundizar más allá de esta skill
Si el usuario pregunta por benchmarks de impacto de la limpieza en modelos ML (paper *CleanML*), por datasets de práctica (Kaggle "Messy Crime Dataset", repo `CleanML` de GitHub), o por diseñar un corpus RAG sobre este dominio, indícale que puedes ampliar con ese detalle bajo pedido — no lo asumas por defecto en cada respuesta.
