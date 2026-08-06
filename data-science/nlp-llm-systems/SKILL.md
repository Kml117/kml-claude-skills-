---
name: nlp-llm-systems
description: Guía operativa para procesamiento de lenguaje natural clásico y moderno, incluyendo modelos de lenguaje (LLMs), RAG y agentes conversacionales. Úsala cuando el usuario trabaje con spaCy, NLTK, Hugging Face Transformers, LangChain/LangGraph, LlamaIndex; necesite diseñar un pipeline RAG, elegir estrategia de chunking, evaluar calidad de respuestas (Ragas), o construir un agente con memoria y herramientas. Aplica también ante dudas sobre embeddings, fine-tuning (LoRA/QLoRA), o búsqueda híbrida (BM25 + vectores).
---

# Procesamiento de Lenguaje Natural (NLP) y Modelos de Lenguaje (LLMs)

## Cuándo se usa esta skill
Cualquier tarea que involucre texto: desde normalización lingüística clásica hasta agentes conversacionales con LLMs y RAG.

*(Si el proyecto es más grande que una consulta puntual — un agente completo, un sistema RAG de producción —, vale la pena pasar primero por `data-science-mentor` para calibrar complejidad y ver si además aplica `mlops-production` o `saas-architect`.)*

## Mapa de decisión de framework

| Necesidad | Framework | Nota |
|---|---|---|
| Preprocesamiento lingüístico rápido a escala industrial (NER, POS tagging, lematización) | **spaCy** | Escrito en Cython; opinado (un algoritmo óptimo por tarea, sin parálisis de decisión) |
| Cargar, ajustar (fine-tune) o servir modelos de deep learning (BERT, Llama, etc.) | **Hugging Face Transformers** | API unificada (`AutoModel`/`AutoTokenizer`); no es la mejor opción para tareas puramente sintácticas de baja latencia |
| Ingesta, fragmentación (chunking) e indexación de documentos para RAG | **LlamaIndex** | Especializado en nodos jerárquicos, fusión de puntuaciones y conectores (LlamaHub) |
| Orquestar un agente con lógica condicional, ciclos, memoria persistente o revisión humana | **LangGraph** | No uses cadenas lineales (`SequentialChain`) para lógica compleja — se vuelve rígida e incapaz de reaccionar a interrupciones de tema |
| Enseñanza/investigación lingüística clásica (WordNet, gramáticas) | **NLTK** | No es para producción de baja latencia ni LLMs modernos |

## La arquitectura estándar de producción (2024-2026)
**LlamaIndex** (ingesta + fragmentación) → **LangGraph** (orquestación del agente, estado y decisiones) → **Ragas** (evaluación continua de fidelidad/relevancia sin necesitar etiquetas humanas). Esta división de responsabilidades minimiza latencia y maximiza precisión semántica.

```python
# Ingesta jerárquica con LlamaIndex
parser = HierarchicalNodeParser.from_defaults(chunk_sizes=[1024, 512, 128])
nodos = parser.get_nodes_from_documents(documentos)
query_engine = VectorStoreIndex(nodos).as_query_engine(similarity_top_k=3)
# El query_engine se registra como una herramienta más dentro de un nodo de LangGraph
```

## Estrategia de chunking: la decisión que más impacta la calidad del RAG
- **Chunking jerárquico parent-child**: fragmentos "hoja" pequeños (~128 tokens) para búsqueda precisa, pero al recuperar uno se inyecta el bloque "padre" completo (~1024 tokens) para dar contexto suficiente al LLM.
- **Recuperación contextual (Contextual Retrieval, Anthropic)**: antepone a cada fragmento un resumen breve generado por un LLM rápido, para que términos ambiguos no se confundan entre dominios.
- **Late Chunking**: calcula los embeddings sobre el documento completo *antes* de cortar en fragmentos — evita perder relaciones de pronombres/referencias cruzadas en textos largos con fórmulas o dependencias extensas.
- **Nunca** fragmentar bloques de código por conteo fijo de caracteres — corta funciones a la mitad. Usa un divisor consciente de sintaxis (AST o `RecursiveCharacterTextSplitter.from_language`).

## Búsqueda híbrida como estándar (no solo vectores)
Combina similitud densa (embeddings) con **BM25** (coincidencia léxica exacta, indispensable para nombres de variables/funciones) y pasa el resultado por un **reranker** (cross-encoder) antes de enviarlo al LLM. Reduce drásticamente la tasa de fallos de recuperación frente a usar solo un método.

## Dónde vive el índice: bases de datos vectoriales

| Si necesitas... | Usa | Nota |
|---|---|---|
| Prototipo rápido, sin infraestructura propia, SaaS gestionado | **Pinecone** | Setup en minutos; costo escala con volumen — revisar antes de comprometer un proyecto grande |
| Open source, self-hosted, control total de infraestructura | **Weaviate** o **Qdrant** | Qdrant destaca en filtrado por metadatos a gran escala; Weaviate trae búsqueda híbrida (BM25+vectores) nativa |
| Ya tienes Postgres en producción y no quieres un sistema nuevo | **pgvector** | Extensión nativa de Postgres; suficiente hasta unos pocos millones de vectores, más simple operativamente que un vector store dedicado |
| Millones/billones de vectores, latencia mínima, presupuesto de infra grande | **Milvus** | Diseñado para escala masiva desde el inicio, mayor complejidad operativa |

**Anti-patrón**: elegir un vector store dedicado (Pinecone/Weaviate) para un proyecto con <100K documentos y que ya corre sobre Postgres — pgvector resuelve el caso sin añadir un sistema distribuido más a la infraestructura.

## Costo de tokens (FinOps de IA)

El costo de un sistema RAG/agente no es fijo — depende de tokens de entrada (contexto recuperado + historial) y salida (generación), y escala con cada llamada. Antes de escalar un prototipo a producción:
- **Mide tokens por interacción real**, no estimados — el contexto recuperado (chunking jerárquico, múltiples documentos) suele ser el mayor costo oculto, no la pregunta del usuario.
- **Cachea agresivamente** cuando el contexto se repite entre llamadas (prompt caching) — reduce costo y latencia en flujos con system prompts largos o RAG con la misma base de conocimiento.
- **Usa un modelo más pequeño/rápido para pasos intermedios** (reranking, resúmenes de chunks para Recuperación Contextual) y reserva el modelo grande para la generación final.
- Esto conecta directo con `mlops-production`: los mismos principios de monitoreo de costo y degradación en producción aplican aquí, solo que el "recurso" es tokens de API en vez de GPU propia.

## Anti-patrones
- **Cadenas rígidas de LangChain para lógica de agente compleja**: usa LangGraph si hay ciclos o decisiones condicionales.
- **Escribir directo en caliente al vector store** sin pipeline controlado: desalinea el linaje del conocimiento y genera vectores redundantes.
- **Envolver una llamada simple de inferencia en capas innecesarias de abstracción**: si es una consulta de un solo paso, usa el SDK nativo, no un framework de orquestación completo.

## Glosario mínimo
- **RAG**: conecta un LLM con una base de conocimiento externa para fundamentar la generación.
- **LoRA / QLoRA**: ajuste fino eficiente que entrena solo matrices de bajo rango (QLoRA además cuantiza el modelo base a 4 bits).
- **Fidelidad (Faithfulness)**: métrica de Ragas — qué fracción de la respuesta está realmente respaldada por el contexto recuperado.
