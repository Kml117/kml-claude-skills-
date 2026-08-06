---
name: data-ethics-governance
description: Guía operativa para ética de datos, gobernanza de datos e IA responsable. Úsala cuando el usuario necesite evaluar sesgos algorítmicos, implementar explicabilidad (SHAP, LIME), cumplir con regulaciones de privacidad (GDPR, LGPD, Ley 1581 Colombia, LFPDPPP México), diseñar un sistema de gestión de IA (ISO 42001), o clasificar el nivel de riesgo de un sistema bajo la EU AI Act. Aplica también ante preguntas sobre variables proxy, fairness, red-teaming de modelos, o gobernanza de agentes autónomos (IA agéntica).
---

# Ética, Gobernanza y Privacidad de Datos

## Cuándo se usa esta skill
Es transversal a todo el ciclo de vida del dato — no es una fase secuencial sino un marco que aplica desde la adquisición hasta el apagado de un modelo en producción.

## ⚠️ Vigencia de este contenido
Esta skill tiene más afirmaciones con fecha que cualquier otra de la colección — es la más expuesta a quedar desactualizada silenciosamente. Antes de dar una fecha límite, estado de vigencia o clasificación de riesgo como definitiva, **verifica con búsqueda web**:
- Plazos de aplicación del EU AI Act (aquí se cita agosto 2026 para alto riesgo — puede haber cambiado o tener prórrogas)
- Estado de las orientaciones de la AEPD sobre IA agéntica (2026, en evolución)
- Reformas en curso (la LFPDPPP de México se menciona "en debate" — puede ya estar resuelta)
- Certificaciones o estándares ISO/NIST citados — verifica la versión vigente antes de recomendarla como la actual

## Mapa de decisión: qué framework usar

| Si el usuario necesita... | Usa | Nota |
|---|---|---|
| Estructurar la gestión de datos de la organización desde cero | **DAMA-DMBOK2** | La "rueda" de 17 capítulos; base sobre la que se construye todo lo demás |
| Medir la madurez real de la gestión de datos (para justificar presupuesto ante la junta) | **DCAM v3** (EDM Council) | 34 capacidades con scoring formal; dominante en sector financiero |
| Mapear riesgos de un sistema de IA de forma flexible y voluntaria | **NIST AI RMF 1.0** | Cuatro funciones: Govern → Map → Measure → Manage; estándar de EE.UU. |
| Obtener una certificación auditable internacional de gestión de IA | **ISO/IEC 42001:2023** | Certificable por terceros; estructura PDCA; compatible con ISO 27001 |
| Alinear la estrategia de IA con principios internacionales de alto nivel | **Principios OCDE de IA (2024)** | Ratificados por 47+ gobiernos; no es operativo para pipeline diario |

## Combinación más común en proyectos reales
**DAMA-DMBOK2** (asegura linaje e integridad de datos) + **NIST AI RMF** (mapea riesgos, evalúa sesgos, prueba explicabilidad). Sin datos gobernados, las métricas de fairness del NIST no tienen base confiable.

**Anti-patrón**: usar NIST AI RMF sin haber establecido primero políticas básicas de calidad y linaje de datos (DAMA) — las evaluaciones de sesgo fallarán porque no sabes de dónde vienen tus datos ni qué transformaciones sufrieron.

## Explicabilidad: SHAP y LIME

- **SHAP** (Shapley Values): asigna la contribución marginal exacta de cada variable a una predicción específica. Basado en teoría de juegos. Úsalo para explicar modelos de caja negra (XGBoost, redes neuronales) tanto a nivel global como local.
- **LIME**: perturba los datos alrededor de una observación y entrena un modelo lineal simple para explicar esa predicción localmente. Más rápido pero menos riguroso que SHAP.

Ambos son herramientas de *explicabilidad post-hoc* — hacen comprensible un modelo opaco, pero no lo vuelven inherentemente interpretable. Para regulaciones que exijan interpretabilidad nativa (EU AI Act en alto riesgo), considerar modelos de "caja de cristal" (regresiones lineales, árboles de decisión simples).

## Sesgos: detección y mitigación

- **Variables proxy**: atributos aparentemente neutrales que correlacionan con características protegidas (código postal → raza, nombre → género). Un modelo puede discriminar indirectamente sin usar la variable protegida directamente.
- **Herramientas**: `Fairlearn` (Microsoft) para auditar paridad demográfica e igualdad de oportunidades; `AIF360` (IBM) para técnicas de mitigación pre/in/post-procesamiento.
- **Flujo**: entrenar modelo → auditar disparidades con Fairlearn → aplicar mitigación (reponderación, restricciones de optimización) → verificar que la precisión se mantenga aceptable.

## Regulación de privacidad: mapa rápido por jurisdicción

| Regulación | Jurisdicción | Autoridad | Punto clave |
|---|---|---|---|
| **GDPR/RGPD** | Unión Europea | Autoridades nacionales de protección de datos | Consentimiento explícito, DPIA obligatorio para alto riesgo, derecho al olvido |
| **LGPD** | Brasil | ANPD | Modelada sobre GDPR; multas severas en vigor |
| **Ley 1581** | Colombia | SIC | Habeas data como derecho constitucional; derechos ARCO |
| **LFPDPPP** | México | INAI | Aviso de privacidad obligatorio; reforma en debate |
| **EU AI Act** | Unión Europea | AESIA (España), autoridades nacionales | Clasifica IA por riesgo (inaceptable/alto/limitado/mínimo); obligaciones de conformidad para alto riesgo desde agosto 2026 |

## IA agéntica: riesgos específicos de gobernanza (tendencia 2025-2026)
Los agentes autónomos con memoria persistente y capacidad de invocar herramientas externas introducen riesgos nuevos: encadenamiento de finalidades del dato no consentidas, inyección indirecta de prompts vía documentos recuperados, y memorias que acumulan datos personales sin control del usuario. La AEPD (España) publicó orientaciones específicas en 2026 — consultarlas ante cualquier diseño de agente con memoria.

## Glosario mínimo
- **DPIA (Data Protection Impact Assessment)**: evaluación proactiva obligatoria del impacto de un tratamiento de datos sobre la privacidad — requerida por GDPR antes de implementar sistemas de alto riesgo.
- **Derechos ARCO**: Acceso, Rectificación, Cancelación y Oposición — facultades legales del ciudadano sobre sus datos personales.
- **Red-teaming de IA**: simulación adversaria donde se intenta romper las protecciones éticas/de seguridad del modelo para encontrar vulnerabilidades antes de que lo haga un atacante real.
- **Alucinación de IA**: cuando un LLM genera información factualmente falsa con tono de certeza — la métrica de fidelidad (faithfulness) de RAGAS mide esto cuantitativamente.

## Integración con csdd-mentor: de hallazgo ético a artículo constitucional

Esta skill no vive aislada del proceso de desarrollo — sus hallazgos deben convertirse en artículos verificables de `constitution.md`, no quedarse como una conversación aparte. Cuando esta skill identifique alguno de estos casos, dile al usuario que active `csdd-mentor` (si no está activa) y traduce el hallazgo así:

| Hallazgo de esta skill | Artículo CSDD que activa |
|---|---|
| El sistema clasifica como alto riesgo bajo EU AI Act, o maneja datos de salud/financieros | Constitución completa obligatoria, no ligera — ver taxonomía de Complejidad en `data-science-foundations` |
| Se detectaron variables proxy o disparidad en Fairlearn/AIF360 | Artículo de **Auditability** (VII): la mitigación aplicada y su verificación deben quedar registradas y ser reproducibles |
| El sistema requiere DPIA (GDPR) o registro ante SIC (Ley 1581) | Artículo de **No Secrets in Code** (VIII) + **Auditability** (VII): el consentimiento y las finalidades del dato son parte del contrato verificable, no solo del aviso de privacidad |
| Es un agente con memoria persistente (IA agéntica) | Artículo de **Least Privilege by Default** (IV) y **Fail Secure** (VI): la memoria del agente nunca debe acumular datos fuera del alcance consentido, y un fallo de permisos debe negar acceso, no exponerlo |

No dupliques el trabajo de `csdd-mentor` explicando los 9 artículos desde cero — solo señala cuál aplica y por qué, y deja que esa skill lleve el proceso de verificación.
