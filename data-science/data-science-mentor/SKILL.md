---
name: data-science-mentor
description: Punto de entrada y diagnóstico para cualquier proyecto de ciencia de datos o machine learning. Actívala cuando el usuario empiece un proyecto nuevo de datos/ML sin especificar la fase ("quiero hacer un proyecto de datos sobre X", "ayúdame con machine learning", "no sé por dónde empezar"), o cuando la petición podría mapear a más de una de las 13 skills especializadas (foundations, data-engineering, cleaning, EDA, ML supervisado/no supervisado, deep learning, NLP/LLMs, visión, evaluación/causalidad, MLOps, ética, storytelling) y no está claro cuál. Decide qué combinación activar, calibra el nivel de rigor, y señala cuándo además hace falta csdd-mentor o saas-architect.
---

# Data Science Mentor — Diagnóstico y Enrutador

## Propósito

Esta skill no enseña ciencia de datos — decide **cuál de las 13 skills especializadas** activar y **cuánto rigor** aplicar, antes de que tú o yo escribamos una sola línea de código o abramos un notebook.

## Las 13 skills y su orden natural en el pipeline

```
1. data-science-foundations   → matemática, stats, entorno (uv/Pixi/DVC), SQL
2. data-engineering-pipelines → adquisición, ETL/ELT, Spark/Airflow/dbt/Kafka
3. data-cleaning-quality      → nulos, outliers, contratos de datos
4. eda-feature-engineering    → perfilado, correlaciones, features, viz exploratoria
5. ml-supervised-forecasting  → regresión/clasificación, series de tiempo
6. ml-unsupervised-reinforcement → clustering, anomalías, recomendación, RL
7. deep-learning-foundations  → redes neuronales, PyTorch/TF/JAX/Keras
8. nlp-llm-systems            → NLP clásico, LLMs, RAG, agentes, vector DBs
9. computer-vision-pipelines  → detección/segmentación, OpenCV/YOLO/SAM2
10. model-evaluation-causality → métricas, A/B testing, inferencia causal
11. mlops-production          → despliegue, monitoreo, reentrenamiento
12. data-ethics-governance    → sesgos, explicabilidad, privacidad (transversal)
13. data-storytelling-bi      → comunicación ejecutiva, dashboards
```

`data-ethics-governance` es transversal — puede activarse en cualquier punto, no solo al final.

## Diagnóstico inicial (hazlo siempre primero)

```
┌─ DIAGNÓSTICO DE PROYECTO DE DATOS ─────────────────────────┐
│ 1. ¿En qué fase está? (o "no sé" → asumimos que arrancamos  │
│    desde cero y empezamos por data-science-foundations)     │
│ 2. ¿Hay variable objetivo etiquetada?                        │
│    SÍ → supervisado/forecasting | NO → no supervisado/RL    │
│ 3. ¿Qué tipo de dato? Tabular / Texto / Imagen-video /       │
│    Series de tiempo — decide si además de ML general entra  │
│    NLP, visión, o forecasting especializado                 │
│ 4. ¿El objetivo es explorar o llegar a producción?           │
│    Explorar → se queda en foundations/cleaning/EDA           │
│    Producción → suma mlops-production                        │
│ 5. ¿Maneja datos sensibles o regulados (salud, financiero,   │
│    biométrico)? → activa data-ethics-governance ya           │
│ 6. ¿Va a servir a múltiples clientes/tenants (ej. Cifra)?    │
│    → activa saas-architect junto con mlops-production        │
│ 7. Complejidad: Simple / Media / Compleja / Enterprise        │
└───────────────────────────────────────────────────────────────┘
```

Si el usuario no puede responder 2-4, no insistas con las 7 preguntas de golpe — infiere de la descripción del problema y confirma tu interpretación en una frase antes de avanzar.

## Tabla de enrutamiento rápido

| El usuario dice... | Activa |
|---|---|
| "tengo estos datos crudos, hay que traerlos/moverlos" | `data-engineering-pipelines` |
| "mis datos tienen nulos/outliers/inconsistencias" | `data-cleaning-quality` |
| "quiero entender qué hay en este dataset" | `eda-feature-engineering` |
| "quiero predecir X" (X es un número o categoría conocida) | `ml-supervised-forecasting` |
| "quiero agrupar/detectar anomalías/recomendar sin etiquetas" | `ml-unsupervised-reinforcement` |
| "quiero entrenar una red neuronal / elegir framework de DL" | `deep-learning-foundations` |
| "quiero un chatbot/RAG/agente sobre texto" | `nlp-llm-systems` |
| "quiero detectar/segmentar objetos en imágenes o video" | `computer-vision-pipelines` |
| "quiero extraer datos estructurados de una imagen/documento" (ej. un recibo, una factura, un formulario escaneado) | `nlp-llm-systems` — es extracción multimodal vía LLM con visión, no detección/segmentación clásica. No actives `computer-vision-pipelines` para esto solo porque hay una imagen de por medio |
| "¿esto realmente funciona? ¿el efecto es real?" | `model-evaluation-causality` |
| "hay que llevar el modelo a producción / ya está en producción y algo falla" | `mlops-production` |
| "¿esto es legal/ético? ¿hay sesgo?" | `data-ethics-governance` |
| "hay que presentárselo a directivos / construir un dashboard" | `data-storytelling-bi` |
| Nada de lo anterior encaja, o es la primera pregunta del proyecto | `data-science-foundations` |

Cuando la petición cruza varias filas (ej. "predecir X y ponerlo en producción"), activa las skills en el orden del pipeline, no todas a la vez de entrada — resuelve modelado primero, luego evaluación, luego MLOps.

## Complejidad: misma escala que en desarrollo de software

Comparte la taxonomía de `modern-dev-stack`/`saas-architect`/`csdd-mentor` — el campo `Complejidad` de la base de Notion `💻 Trazabilidad de Software` usa exactamente estos 4 valores:

| Complejidad | Qué implica en un proyecto de datos | ¿CSDD? | ¿Otras skills obligatorias? |
|---|---|---|---|
| 🟢 **Simple** | Notebook exploratorio, análisis puntual, sin despliegue ni datos sensibles | No | Ninguna adicional |
| 🟡 **Media** | Modelo que va a producción interna, sin datos regulados, un solo consumidor | CSDD ligero si acaso | `mlops-production` si hay despliegue |
| 🟠 **Compleja** | Modelo en producción con usuarios reales, o multi-tenant, o datos moderadamente sensibles | CSDD completo | `mlops-production` + `saas-architect` si multi-tenant |
| 🔴 **Enterprise** | Datos regulados (salud/financiero), alto riesgo bajo EU AI Act, o multi-tenant con clientes pagando (ej. Cifra) | CSDD completo sin atajos | `mlops-production` + `saas-architect` + `data-ethics-governance` obligatoria |

**No apliques rigor de Enterprise a un notebook exploratorio de una tarde** — es tan sobre-ingeniería como pedirle `constitution.md` a una landing page. Calibra hacia abajo con la misma disciplina que hacia arriba.

## Integración con el resto del ecosistema

- **`csdd-mentor`**: se activa completo a partir de Complejidad Compleja/Enterprise, o antes si `data-ethics-governance` detecta un hallazgo que lo amerita (ver la tabla de mapeo hallazgo→artículo en esa skill).
- **`saas-architect`**: se activa si el proyecto sirve modelos a múltiples clientes — ver la sección "Integración con saas-architect" dentro de `mlops-production` para el detalle de aislamiento y FinOps por tenant.
- **`modern-dev-stack`**: si el proyecto de datos necesita además una aplicación web (dashboard custom, API que sirve el modelo), esa parte del stack se diseña con `modern-dev-stack`, no reinventando frontend/backend aquí.
- **`cifra-playbook`**: si el proyecto es específicamente un engagement de Cifra (cliente pagando por DSaaS), esa skill ya secuencia cuándo activar esta y las demás — actívala primero en vez de improvisar el orden aquí.

## Registro en Notion

Bases del workspace "Development" (nombres exactos):

- `💻 Trazabilidad de Software` — el proyecto de datos es una fila con `Tipo de desarrollo` = `📊 Proyecto de Datos / ML`
- `✅ Tareas` — usa la propiedad `Fase ML` (Fundamentos/Setup, Data Engineering, Limpieza/Calidad, EDA/Features, Modelado, Evaluación/Causalidad, MLOps/Deploy, Ética/Gobernanza, Comunicación/BI) para marcar en qué etapa del pipeline está cada tarea, y `Tipo de tarea` = `📊 Análisis de Datos` o `🤖 Modelado ML` según corresponda
- `📐 Documentos CSDD` — igual que en desarrollo de software, aquí van los artefactos si la Complejidad amerita CSDD

Si el MCP de Notion está disponible, ofrece crear o actualizar la fila del proyecto directamente en vez de solo describir qué registrar.

## Anti-patrón de esta skill

Activar las 13 skills de golpe "por si acaso" en la primera respuesta. Actívalas en el orden del pipeline conforme la conversación avanza — cargar las 13 a la vez diluye el contexto y aumenta el riesgo de que dos skills den una respuesta ligeramente contradictoria sobre el mismo tema (ej. qué métrica usar).
