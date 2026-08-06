---
name: ml-unsupervised-reinforcement
description: Guía operativa para machine learning no supervisado (clustering, reducción de dimensionalidad, detección de anomalías, sistemas de recomendación) y aprendizaje por refuerzo (RL). Úsala cuando el usuario mencione PyOD, detección de outliers/anomalías, Gymnasium, Stable-Baselines3, Ray RLlib, d3rlpy, Q-Learning, políticas, MDP, factorización de matrices (implicit), o cuando necesite diseñar un agente que aprenda por interacción con un entorno, online u offline.
---

# Machine Learning No Supervisado y Aprendizaje por Refuerzo

## Cuándo se usa esta skill
No hay variable objetivo etiquetada (no supervisado) o el problema es de decisión secuencial con recompensa (RL).

## Detección de anomalías: PyOD
Suite unificada (>60 algoritmos) con API estilo scikit-learn (`fit`, `decision_function`, `predict_proba`).
- **ADEngine**: motor que selecciona automáticamente el mejor detector según las propiedades del dataset — úsalo cuando no sepas de antemano qué algoritmo aplicar en vez de adivinar uno solo.
- **SUOD**: acelera/paraleliza cuando corres muchos detectores a la vez.
- **PyThresh**: define el umbral de corte de forma estadística en vez de fijar un porcentaje de contaminación arbitrario.

**Anti-patrón crítico**: aplicar detectores basados en distancia (KNN, Local Outlier Factor) sin **escalar/normalizar** las variables antes. La variable de mayor magnitud numérica domina la distancia y oculta anomalías reales en las demás dimensiones — es el error #1 documentado en la práctica de PyOD.

## Recomendación no supervisada: `implicit`
Factorización de matrices (ALS, BPR) para retroalimentación implícita (clics, compras), optimizado en Cython/GPU.
- **Combinación ganadora**: `implicit` (entrena factores latentes) + `Faiss` (indexa esos vectores para búsqueda aproximada de vecinos) → recomendaciones en milisegundos sobre catálogos de millones de ítems.

## Aprendizaje por Refuerzo: qué framework usar

| Necesidad | Framework | Nota |
|---|---|---|
| Definir el entorno (interfaz estándar `reset()`/`step()`) | **Gymnasium** | Sucesor oficial de OpenAI Gym (ya descontinuado) — es el estándar de entrada obligatorio |
| Entrenar un agente en un solo proceso, rápido y confiable | **Stable-Baselines3** | API estilo scikit-learn sobre PyTorch; PPO/SAC/DQN listos para usar |
| Escalar a cientos/miles de núcleos, o multiagente | **Ray RLlib** | No uses SB3 para multiagente instanciando varias políticas en bucle — se satura; RLlib está diseñado nativamente para esto |
| Aprender de datos históricos sin poder explorar en vivo (seguridad crítica: robótica, salud, finanzas) | **d3rlpy (Offline RL)** | Algoritmos como CQL/IQL evitan que el agente extrapole acciones fuera de la distribución de datos observada |

**Combinación ganadora para robótica industrial**: entrenar offline con `d3rlpy` sobre telemetría histórica (seguro, sin tocar la máquina real) → transferir la política a un entorno `Gymnasium` simulado para *fine-tuning* online controlado.

## Anti-patrón transversal de RL
Multiplicar instancias de Stable-Baselines3 para simular multiagente: SB3 asume un único agente por diseño; hacerlo igual genera cuellos de botella de sincronización y colapso del aprendizaje. La señal de que necesitas RLlib (o PettingZoo + RLlib) es justamente esa.

## Glosario mínimo
- **MDP (Proceso de Decisión de Markov)**: el futuro depende solo del estado y la acción actuales, no del historial completo.
- **Ecuación de Bellman**: valor de un estado = recompensa inmediata + valor descontado del siguiente estado.
- **Exploración vs. explotación**: el dilema central de cualquier política de RL.
- **Offline RL / CQL**: entrenar sin interactuar con el entorno real; CQL penaliza acciones fuera de lo observado para evitar extrapolaciones peligrosas.

## Cuándo escalar la conversación
Si el usuario pide RLHF (alineación de LLMs) o robótica con simuladores físicos de alta fidelidad, son extensiones de este dominio pero con matices propios — profundiza puntualmente en vez de asumir que aplican las mismas reglas de RL clásico sin más contexto.
