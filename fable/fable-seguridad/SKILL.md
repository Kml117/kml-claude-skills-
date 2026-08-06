---
name: fable-seguridad
description: Chequeo de seguridad estilo Fable 5. Usar esta skill SIEMPRE antes de finalizar, entregar, publicar, enviar o compartir cualquier proyecto — código, documento legal, presentación, dataset, correo formal o paquete de archivos. Verifica riesgos de seguridad técnica, exposición de datos personales o sensibles (nombres, cédulas, direcciones, números de predio, cuentas, credenciales) y riesgos legales o de distribución. Activar también cuando el usuario mencione "revisar seguridad", "antes de publicar", "antes de enviar", "¿está listo para entregar?" o cuando el trabajo esté a punto de salir de manos del usuario hacia terceros, aunque no pida seguridad explícitamente.
---

# Fable Seguridad — Chequeo antes de entregar

Este manual codifica cómo Fable 5 verifica la seguridad de un trabajo antes de darlo por terminado. El principio central: **la seguridad no es una lista de vulnerabilidades, es una pregunta sobre consecuencias.** Antes de revisar detalles, entiende qué está en juego.

## Paso 0 — Contexto de consecuencias (obligatorio, antes de cualquier checklist)

Responde (o pregunta al usuario) estas tres cosas. Sin ellas, cualquier chequeo es teatro:

1. **¿Qué es lo peor que puede pasar si esto se filtra, falla o llega a la persona equivocada?** Sé concreto: pérdida de dinero, ventaja para una contraparte legal, daño reputacional, exposición de un tercero.
2. **¿Quién tiene acceso ahora y quién debería tenerlo?** La brecha entre esas dos respuestas es el riesgo.
3. **¿Es reversible?** Un correo enviado, un documento radicado o un dato publicado no se puede recoger. Lo irreversible exige el doble de escrutinio.

## Las 4 capas de revisión

Revisa las que apliquen al tipo de trabajo. Nunca asumas que una capa "no aplica" sin decir por qué.

### Capa 1 — Técnica (código, sistemas, apps, scripts)
- Credenciales, API keys o tokens escritos en el código o en el historial.
- Dependencias desactualizadas o con vulnerabilidades conocidas.
- Validación de inputs: ¿qué pasa si alguien mete datos malformados o maliciosos?
- Permisos: ¿el sistema pide más acceso del que necesita?
- Endpoints o datos expuestos sin autenticación.
- Datos de prueba reales (nombres, cédulas reales usados como "ejemplo").

> Esta capa es una revisión manual rápida. Si el entregable es una **base de código sustancial** (no un script suelto), activar la skill `cybersecurity` para el escaneo profundo (8 agentes, OWASP/CWE) y usar su reporte como insumo de esta capa — no repetir a mano lo que esa skill automatiza. Si el proyecto tiene `constitution.md` (CSDD), esa skill además mapea los hallazgos a los artículos constitucionales.

### Capa 2 — Datos personales y sensibles
- Identificadores directos: cédulas, nombres completos, direcciones, teléfonos, correos, matrículas inmobiliarias, números de cuenta.
- Identificadores indirectos: combinaciones que permiten identificar a alguien aunque no haya nombre (vereda + predio + fecha, por ejemplo).
- Pregunta clave: **¿este contenido circula más allá de las partes que ya conocen estos datos?** Si sí, ¿qué debe anonimizarse, redactarse o restringirse?
- Datos de terceros que no dieron consentimiento para aparecer.
- Marco aplicable en Colombia: **Ley 1581 de 2012** — el tratamiento de datos personales de terceros requiere autorización del titular; si el entregable procesa datos personales para un cliente (ej. Cifra), verificar que exista contrato de Encargado de Datos antes de entregar.

### Capa 3 — Legal y documental
- ¿El documento cumple los requisitos formales exigidos (firmas, fechas, anexos, notariado, términos procesales)? Un documento formalmente defectuoso es un riesgo, no solo un error.
- ¿Contiene cifras, cláusulas, estrategias o posiciones de negociación que no deban conocerse prematuramente por la contraparte?
- ¿Hay afirmaciones que puedan usarse en contra del usuario si el documento se filtra o se aporta como prueba?
- ¿Compromete a terceros (familiares, socios, vecinos) sin su conocimiento?

### Capa 4 — Publicación y distribución
- ¿A quién llega exactamente? (destinatario directo + a quién puede reenviarlo).
- ¿El canal es apropiado? (correo vs. radicación formal vs. mensaje informal).
- Metadatos: los archivos Word/PDF pueden contener autor, historial de cambios, comentarios ocultos. Verificar antes de enviar.
- Versiones: ¿se está enviando la versión final o un borrador con notas internas?

## Formato de salida obligatorio

Presenta los hallazgos en una tabla:

| Hallazgo | Severidad | Por qué importa | Acción recomendada |
|---|---|---|---|
| (concreto, citando dónde) | Crítico / Alto / Medio / Bajo | Consecuencia real, no abstracta | Paso específico y ejecutable |

Después de la tabla, un veredicto en una línea: **"Listo para entregar"**, **"Listo con correcciones menores"** o **"No entregar hasta resolver los críticos"**.

## Reglas de oro

1. **Nunca marcar algo como "seguro" sin justificar el porqué.** "No encontré problemas" debe ir acompañado de "revisé X, Y y Z".
2. **Severidad = probabilidad × consecuencia.** Un riesgo improbable pero catastrófico (filtrar una estrategia legal) puede ser crítico; un riesgo frecuente pero trivial puede ser bajo.
3. **Si falta contexto para evaluar una capa, decirlo explícitamente** en lugar de omitir la capa en silencio.
4. **La ausencia de hallazgos críticos no es aprobación entusiasta** — es "no encontré razones para detener la entrega", que es distinto.
