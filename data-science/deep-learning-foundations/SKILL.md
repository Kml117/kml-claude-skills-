---
name: deep-learning-foundations
description: Guía operativa de fundamentos y arquitecturas de deep learning (redes neuronales, CNN, RNN/LSTM, Transformers) y su ecosistema de frameworks (PyTorch, TensorFlow, JAX, Keras 3). Úsala cuando el usuario elija un framework de entrenamiento, tenga dudas sobre backpropagation, funciones de activación, optimizadores, compilación (torch.compile, XLA/JIT), o necesite escalar entrenamiento distribuido con Accelerate/DeepSpeed o JAX/Equinox/Optax.
---

# Deep Learning: Fundamentos, Arquitecturas y Frameworks

## Cuándo se usa esta skill
Diseño, entrenamiento u optimización de redes neuronales — sin entrar todavía en las aplicaciones específicas de NLP o visión (esas son skills aparte).

*(Si no sabes por dónde arrancar un proyecto de datos nuevo, o cuánto rigor aplicarle, empieza por `data-science-mentor`.)*

## ⚠️ Vigencia de este contenido
Certificaciones y "estándar de facto" son afirmaciones que caducan rápido. Verifica antes de citar como vigente:
- Qué certificación de frameworks de DL es la de referencia actual (aquí se cita PTCA tras la descontinuación del TF Developer Certificate)
- Si PyTorch sigue siendo el estándar dominante en investigación, o si el panorama cambió

## Mapa de decisión de framework

| Si el usuario... | Usa | Nota |
|---|---|---|
| Investiga, hace fine-tuning, o usa el ecosistema Hugging Face | **PyTorch** | Estándar de facto en investigación y LLMs 2024-2026; ejecución eager, depuración nativa con `pdb` |
| Despliega en producción corporativa, móvil, embebido o navegador | **TensorFlow** (LiteRT, TF.js) | Mantiene fuerza en infraestructura industrial madura, aunque perdió tracción en investigación |
| Quiere portar el mismo modelo entre PyTorch/TensorFlow/JAX sin reescribirlo | **Keras 3** | Backend intercambiable vía `KERAS_BACKEND`; ideal para prototipar en uno y desplegar en otro |
| Hace investigación algorítmica de frontera, simulación física, o necesita diferenciación de orden alto | **JAX** | Programación funcional pura; compilación JIT extrema con XLA; requiere funciones sin efectos secundarios |

## Combinaciones ganadoras
- **PyTorch + Hugging Face Accelerate + DeepSpeed**: la combinación dominante en la industria 2024-2026 para fine-tuning de modelos grandes en múltiples GPUs — Accelerate elimina el código repetitivo de dispositivo/precisión, DeepSpeed optimiza memoria (ZeRO) para modelos que no caben en una sola GPU.
- **JAX + Equinox + Optax**: para investigación pura y PINNs (redes informadas por física) — Equinox trata la red como un PyTree inmutable, Optax resuelve la optimización.

## Anti-patrones que rompen el rendimiento silenciosamente
- **Operaciones in-place (`x += y`, `inplace=True`) junto con `torch.compile`**: rompen el rastreo de TorchDynamo y fuerzan "graph breaks" — el modelo cae a modo eager lento sin que sea obvio por qué.
- **Mutar estado dentro de `jax.jit` o `jax.vmap`**: JAX exige funciones puras e inmutabilidad estricta; una clase que modifica `self.pesos` dentro de una función compilada genera comportamiento errático silencioso, no un error claro.
- **Capas de TensorFlow (ej. `TextVectorization`) dentro de un modelo Keras 3 con backend PyTorch/JAX**: ciertas capas heredadas siguen dependiendo internamente de TF y rompen la portabilidad multi-backend — aíslalas fuera de la topología del modelo.

## Flujo mental para diagnosticar problemas de entrenamiento
1. **¿El gradiente desaparece?** → revisar función de activación (ReLU en vez de Sigmoide/Tanh en capas profundas) y considerar arquitecturas con conexiones residuales o compuertas (LSTM).
2. **¿Sobreajuste?** → Dropout, regularización, más datos o aumentos — no solo "más épocas".
3. **¿Lento en GPU?** → verificar que no haya operaciones bloqueando la compilación (ver anti-patrones arriba) antes de asumir que hace falta más hardware.

## Glosario mínimo
- **Retropropagación**: aplica la regla de la cadena hacia atrás para calcular gradientes eficientemente.
- **Transformer / Autoatención**: cada elemento de una secuencia se correlaciona con todos los demás — reemplaza la recurrencia y permite paralelismo masivo.
- **Optimizadores de segundo orden (ej. Sophia)**: usan la curvatura (Hessiano) para converger más rápido que Adam/AdamW en ciertos escenarios de pre-entrenamiento de LLMs.

## Nota de contexto
El *TensorFlow Developer Certificate* fue descontinuado por Google en mayo de 2024; la certificación vigente de referencia en la industria es la *PyTorch Certified Associate (PTCA)* de la Linux Foundation.
