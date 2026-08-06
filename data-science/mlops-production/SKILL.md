---
name: mlops-production
description: Guía operativa para MLOps — despliegue, monitoreo y mantenimiento de modelos en producción. Úsala cuando el usuario necesite llevar un modelo a producción, elegir entre MLflow/Kubeflow/BentoML/Triton, diseñar un Feature Store (Feast), detectar model drift, automatizar reentrenamiento, o decidir estrategias de despliegue (canary, shadow, blue-green). Aplica también ante "mi modelo funciona en el notebook pero no en producción" o "el rendimiento del modelo se degradó silenciosamente".
---

# MLOps: Despliegue, Monitoreo y Producción de Modelos

## Cuándo se usa esta skill
El modelo ya está entrenado y validado. Ahora hay que servirlo, monitorearlo y mantenerlo vivo — porque los modelos de ML fallan *silenciosamente* (siguen respondiendo HTTP 200 mientras sus predicciones se degradan).

## ⚠️ Vigencia de este contenido
Verifica antes de recomendar como estado actual:
- Si la integración DVC + lakeFS (citada desde 2025) sigue siendo la combinación recomendada o evolucionó
- Si MLflow/BentoML/Triton siguen siendo los líderes de sus categorías o surgió una alternativa dominante

## Mapa de decisión de framework

| Necesidad | Framework | Nota clave |
|---|---|---|
| Registrar experimentos, versionar modelos, promover a producción | **MLflow** | Tracking + Model Registry + Gateway para LLMs; estándar de Databricks y Linux Foundation |
| Versionar datos pesados y modelos binarios junto con el código | **DVC** | Flujo completo en `data-science-foundations`; aquí solo aplica: integrado con lakeFS desde 2025 para versionar lagos de datos completos |
| Empaquetar un modelo como microservicio REST/gRPC listo para Docker | **BentoML** | Python-first; separa I/O de cómputo GPU; genera imágenes OCI con un `bentofile.yaml` |
| Servir con máxima eficiencia en GPU (dynamic batching, multi-modelo) | **NVIDIA Triton** | Estándar para inferencia de alta demanda; soporta ONNX/TensorRT/PyTorch simultáneamente |
| Orquestar pipelines ML completos en Kubernetes | **Kubeflow** | Solo si tienes equipo de infra K8s dedicado; no lo uses en proyectos pequeños — la complejidad te come |
| Servir y materializar features en tiempo real para inferencia | **Feast** | Feature Store open source; evita training-serving skew con Point-in-Time correctness |
| Detectar drift de datos/conceptos sin tener etiquetas de verdad | **Evidently AI** | +100 métricas estadísticas; Test Suites para CI/CD; integra con Prometheus/Grafana |

## La combinación dominante en producción (equipos sin K8s dedicado)
`Git + DVC` (versiona código + datos) → `MLflow` (registra experimentos y modelos) → `BentoML` (empaqueta como Docker) → `Evidently AI` (monitorea drift) → `Prometheus/Grafana` (alertas visuales). Todo orquestado por `Prefect` o `Airflow`.

## Combinaciones especializadas

**Servido de baja latencia**: `Feast` (materializa features en Redis en microsegundos) → `BentoML` (recibe petición, consulta Feast) → `Triton` (ejecuta inferencia en GPU con dynamic batching). Para e-commerce, recomendación en tiempo real.

**Kubernetes nativo**: `Kubeflow Pipelines` (orquesta entrenamiento) → `KServe` (sirve con autoescalado/scale-to-zero) → `Prometheus` (métricas del clúster). Para corporaciones con infra K8s consolidada.

## Estrategias de despliegue
- **Canary**: envía 5% del tráfico al modelo nuevo; si las métricas se mantienen, escala gradualmente.
- **Shadow**: el modelo nuevo recibe 100% del tráfico real pero sus respuestas no se entregan al usuario — solo se evalúan.
- **Blue-Green**: dos infraestructuras idénticas en paralelo; se conmuta el router de golpe.
- **Rollback**: si algo falla, revertir inmediatamente al modelo anterior registrado en MLflow Model Registry.

## Anti-patrones

- **Git LFS para datasets iterativos de ML**: ver el anti-patrón completo en `data-science-foundations` — aquí aplica igual, agravado porque en producción además pierdes comparación de métricas entre versiones de modelo.
- **Great Expectations para detectar drift**: GX hace aserciones estáticas de calidad, no calcula distribuciones probabilísticas dinámicas ni tests de hipótesis por ventanas temporales — usa Evidently AI.
- **Servir modelos con FastAPI crudo sin batching**: para baja carga funciona, pero bajo tráfico real la GPU pasa la mayor parte del tiempo ociosa procesando peticiones una a una — Triton o BentoML resuelven esto con dynamic batching nativo.

## Glosario mínimo
- **Data Drift**: la distribución de las entradas cambió respecto al entrenamiento (ej. los usuarios ahora son de otro país).
- **Concept Drift**: la relación real entre entradas y output cambió (ej. el comportamiento de compra post-pandemia ya no sigue los patrones de 2019).
- **Feature Store**: repositorio centralizado de variables estadísticas compartidas entre modelos; Feast garantiza que en inferencia se usen las mismas transformaciones que en entrenamiento (evita training-serving skew).
- **Dynamic Batching**: Triton agrupa peticiones individuales en lotes de milisegundos para maximizar el uso del GPU.
- **Point-in-Time Correctness**: Feast solo recupera features que existían *antes* del momento de predicción — evita fuga de datos del futuro.

## Nota de contexto
El paper "Hidden Technical Debt in Machine Learning Systems" (Google, NeurIPS 2015) sigue siendo la referencia fundacional: el código del modelo es una fracción mínima del sistema; el 90% es infraestructura de datos, monitoreo, serving y reentrenamiento. MLOps existe para gestionar ese 90%.

## Integración con saas-architect: servir modelos multi-tenant

Si el modelo se sirve a múltiples clientes (ej. un negocio de Data Science as a Service), esto deja de ser solo MLOps y se vuelve una decisión de arquitectura SaaS — activa `saas-architect` junto con esta skill:

- **Aislamiento de modelos por tenant**: ¿un modelo compartido con features por cliente, o un modelo/versión dedicada por cliente? Mapea directo a la decisión Pool vs Silo de `saas-architect`, aplicada a inferencia en vez de a base de datos.
- **Atribución de costo de GPU/inferencia por tenant (FinOps)**: sin esto, un cliente con tráfico alto subsidia su costo con el de los demás sin que nadie lo note hasta la factura. Etiqueta cada request con `tenant_id` desde el gateway (BentoML/Triton) hacia el sistema de métricas.
- **Feature Store multi-tenant**: si usas Feast, las features materializadas necesitan namespace por tenant igual que cualquier tabla en el modelo Pool de `saas-architect` — un feature calculado con datos de un cliente nunca debe filtrarse a la inferencia de otro.
- **Rate limiting por tenant**: mismo patrón de "noisy neighbor" que en SaaS general — un cliente con picos de tráfico no debe degradar la latencia de inferencia de los demás.

Si el proyecto maneja datos regulados (salud, financiero) además de ser multi-tenant, esto también activa `csdd-mentor` completo — ver la sección de integración en `data-ethics-governance`.
