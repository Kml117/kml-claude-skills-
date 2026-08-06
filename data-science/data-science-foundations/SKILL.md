---
name: data-science-foundations
description: Guía operativa de fundamentos matemático-estadísticos y herramientas core para ciencia de datos. Úsala cuando el usuario necesite ayuda con probabilidad, estadística inferencial, álgebra lineal aplicada, o con la elección/configuración de entornos de trabajo (uv, Pixi, Conda, Jupyter, Git, DVC, DuckDB). Aplica también ante preguntas sobre gestión de entornos virtuales, control de versiones de datos, o cuando alguien mezcle pip y conda sin control y rompa su entorno.
---

# Fundamentos: Estadística, Probabilidad y Herramientas Core

## Cuándo se usa esta skill
Antes de modelar: las bases matemáticas que sostienen todo lo demás, y el ecosistema de herramientas que hace que un proyecto sea reproducible.

*(Si no sabes por dónde arrancar un proyecto de datos nuevo, o cuánto rigor aplicarle, empieza por `data-science-mentor` — hace el diagnóstico inicial y decide qué combinación de estas 13 skills activar.)*

## Mapa de decisión: gestión de entornos

| Si el usuario... | Usa | Nota |
|---|---|---|
| Trabaja solo en Python, sin dependencias binarias del sistema | **uv** | Escrito en Rust, ~100x más rápido que pip; genera `uv.lock` determinista multiplataforma |
| Necesita CUDA, GDAL, compiladores C++, o mezcla Python+R | **Pixi** | Resuelve canales conda-forge + PyPI en un solo `pixi.toml`; usa uv internamente para PyPI |
| Ya tiene un ecosistema Conda consolidado y no quiere migrar | **Conda** | Sigue siendo válido, pero el solver clásico es lento — Pixi lo moderniza |
| Quiere exploración interactiva rápida con narrativa + código | **Jupyter** (dentro de un entorno aislado) | No lo uses para código de producción: el JSON no lineal dificulta Git y promueve desorden de ejecución |

**Anti-patrón #1 más común**: hacer `conda install X` y luego `pip install Y` sin aislamiento dentro del mismo entorno — corrompe el grafo de dependencias de forma irreversible. Usa Pixi (que resuelve ambos mundos) o aísla estrictamente.

## Control de versiones: Git + DVC

Git versiona el código; DVC versiona los datos pesados y los modelos binarios. DVC guarda archivos `.dvc` ligeros (hashes) en Git y los datos reales en almacenamiento remoto (S3, GCS, Azure Blob).

Flujo correcto: `git init` → `dvc init` → `dvc add data/train.csv` → `git add data/train.csv.dvc` → `dvc push`. Para reproducir: `git clone` + `dvc pull`.

**Anti-patrón**: usar Git LFS para datasets que cambian en cada iteración de entrenamiento — no tiene integración con pipelines ML, no cachea ejecuciones previas, y degrada el repo.

*(En producción, DVC se combina con MLflow para el registro de modelos y con lakeFS para versionar lagos de datos completos — ver `mlops-production`.)*

## Analítica local rápida: DuckDB

Motor columnar embebido (no necesita servidor) que corre consultas OLAP directamente sobre archivos Parquet/CSV en memoria. Más rápido que pandas para agregaciones sobre datasets de varios GB. Ideal dentro de Jupyter para exploración sin levantar infraestructura cloud.

## Fundamentos de SQL

Es la herramienta que más se subestima como "básica" y más se usa realmente en el día a día — antes de tocar pandas, la mayoría de los datos ya pasaron por una consulta SQL.

- **Orden de ejecución real** (no el orden en que se escribe): `FROM` → `WHERE` → `GROUP BY` → `HAVING` → `SELECT` → `ORDER BY` → `LIMIT`. Entender esto explica por qué no puedes usar un alias de `SELECT` en el `WHERE` de la misma consulta.
- **JOINs**: `INNER` (solo coincidencias) vs `LEFT` (todo lo de la izquierda + coincidencias) — el error más común es un `INNER JOIN` que descarta filas sin darte cuenta, inflando o reduciendo silenciosamente el conteo de filas.
- **Funciones de ventana** (`ROW_NUMBER()`, `RANK()`, `LAG()/LEAD()`, promedios móviles con `OVER (PARTITION BY ... ORDER BY ...)`): reemplazan la mayoría de los casos donde alguien recurre a un loop manual.
- **CTEs (`WITH ... AS`)** en vez de subconsultas anidadas: mismo resultado, legible y depurable paso a paso.

Para el uso de SQL dentro de pipelines de transformación en el warehouse (la "T" de ELT, tests declarativos), la extensión natural de esto vive en `data-engineering-pipelines` (dbt).

## Fundamentos matemáticos: qué cubrir y en qué orden

1. **Estadística descriptiva** (media, mediana, varianza, desviación estándar) — la base de cualquier EDA.
2. **Probabilidad y distribuciones** (normal, binomial, Bayes) — necesarias para entender inferencia y modelos generativos.
3. **Inferencia estadística** (hipótesis nula, p-valor, intervalos de confianza) — base de experimentación y validación.
4. **Álgebra lineal aplicada** (vectores, matrices, eigenvalores, producto interno, proyecciones ortogonales) — base de PCA, embeddings, redes neuronales.
5. **Cálculo multivariable** (derivadas parciales, gradiente, descenso de gradiente) — base de optimización de modelos.
6. **Regularización** (Lasso L1 / Ridge L2) — controla sobreajuste; Lasso hace selección de variables (coeficientes a cero), Ridge estabiliza ante multicolinealidad.

## Glosario mínimo
- **Gradiente**: vector de derivadas parciales que apunta en la dirección de máximo incremento de una función — el descenso de gradiente va en la dirección opuesta para minimizar la pérdida.
- **PCA**: proyecta datos en ejes de varianza máxima (eigenvalores/eigenvectores de la matriz de covarianza).
- **Archivo de bloqueo (lockfile)**: registro exacto de versiones de cada paquete instalado — garantiza que el entorno se recree idéntico en cualquier máquina.
- **Ejecución vectorial**: DuckDB procesa columnas enteras en paralelo en vez de fila por fila — por eso es más rápido que pandas para agregaciones.

## Stack más común en proyectos reales (2024-2026)
Python gestionado con **uv**, código en **Git** + datos en **DVC**, exploración interactiva en **Jupyter** con **DuckDB** para consultas analíticas locales sobre Parquet.
