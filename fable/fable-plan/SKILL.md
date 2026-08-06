---
name: fable-plan
description: Preguntas de planeación estilo Fable 5. Usar esta skill SIEMPRE que se necesite construir un plan de desarrollo para un proyecto, función, documento, trámite o iniciativa — genera las preguntas que Fable 5 haría antes de planear, para evitar planes vagos, incompletos o imposibles de verificar. Activar cuando el usuario pida "ayúdame a planear", "hazme un plan", "cómo desarrollo esto", "qué pasos debo seguir", o presente un objetivo claro pero sin ruta de ejecución. También activar cuando otro plan existente parezca vago (tareas sin criterio de verificación, sin plazos, sin responsables).
---

# Fable Plan — Las preguntas antes del plan

Este manual codifica las preguntas que Fable 5 se hace (o le hace al usuario) antes de escribir un plan. El principio central: **un plan es tan bueno como las preguntas que lo precedieron.** Un plan hecho sin estas respuestas es una lista de deseos con viñetas.

**Frontera con fable-arranque:** si el objetivo, destinatario o Definición de Terminado aún no existen, eso es territorio de `fable-arranque` — correr ese diagnóstico primero. Si arranque ya corrió en esta conversación, heredar sus respuestas: las categorías 1-4 de abajo probablemente ya están respondidas, saltar directo a dependencias, riesgos y secuenciación.

## Cómo usar el banco de preguntas

No es un interrogatorio. Fable 5 primero **responde solo las que puede** con el contexto disponible, luego pregunta al usuario únicamente las que quedan abiertas y son críticas. Las no críticas se convierten en supuestos explícitos dentro del plan.

## Banco de preguntas por categoría

### 1. Propósito y criterio de éxito
- ¿Qué problema resuelve esto, exactamente y para quién?
- ¿Cómo sabremos, con evidencia observable, que funcionó? ("Quedó bien" no es evidencia; "el banco aprobó el crédito" sí.)
- ¿Qué pasaría si no se hace nada? (Si la respuesta es "casi nada", el plan quizás no vale el esfuerzo.)

### 2. Alcance
- ¿Qué incluye explícitamente?
- ¿Qué queda fuera **a propósito**? (Escribirlo evita que el proyecto crezca sin control.)
- ¿Cuál es la versión mínima que ya sirve, y cuál es la versión ideal?

### 3. Usuarios y beneficiarios reales
- ¿Quién lo usa o lo recibe, con nombre o rol concreto?
- ¿Qué necesita esa persona específicamente — no en general, sino en este caso?
- ¿Quién puede oponerse o verse afectado negativamente?

### 4. Restricciones duras
- Tiempo: ¿hay fechas límite externas (términos legales, convocatorias, cosechas, semestres)? Anotarlas con fecha exacta.
- Normativa: ¿qué reglas aplican y cuáles son innegociables?
- Recursos: presupuesto, personas, herramientas, acceso a información.

### 5. Dependencias y bloqueos
- ¿Qué debe existir antes de que esto pueda avanzar? (documentos, aprobaciones, respuestas de terceros, dinero)
- ¿De quién depende cada cosa? Las dependencias de terceros son las que más retrasan: identificarlas y activarlas primero.
- ¿Cuál es el verdadero cuello de botella? (Casi nunca es la tarea más grande; suele ser la que depende de alguien más.)

### 6. Riesgos y plan B
- ¿Qué es lo más probable que falle? (una respuesta, no cinco)
- ¿Qué es lo más grave que puede fallar, aunque sea improbable?
- Para cada uno: ¿qué se hace si pasa? Un riesgo sin respuesta no está gestionado, está ignorado.

### 7. Reversibilidad
- Si esto resulta ser un error, ¿qué tan costoso es deshacerlo?
- Las decisiones irreversibles (radicar una demanda, firmar, publicar, gastar) van al final de la secuencia siempre que sea posible, con un punto de verificación justo antes.

### 8. Secuenciación
- ¿Cuál es el orden correcto? Criterio: primero lo que desbloquea a terceros, luego lo irreversible al final, y lo incierto lo antes posible (para descubrir problemas temprano, cuando corregir es barato).
- ¿Qué puede hacerse en paralelo?

## Formato del plan resultante

El output NO es una lista de tareas. Es una secuencia de **hitos**, cada uno con:

| Hito | Qué se entrega | Criterio de verificación | Depende de | Fecha objetivo |
|---|---|---|---|---|

Más una sección final: **Supuestos [VALIDAR]** y **Riesgos con respuesta**.

Si el proyecto es de software/datos y vive en el sistema de Notion del usuario (workspace "Development"), ofrecer registrar los hitos como tareas en `✅ Tareas` con su `Fecha límite` y relación al desarrollo — sin forzarlo para proyectos de otros dominios (legales, comunitarios, familiares).

## Reglas de oro

1. **Todo hito debe ser verificable por un tercero.** Si nadie más puede confirmar que se cumplió, no es un hito.
2. **Lo incierto primero, lo irreversible al final.**
3. **Un plan sin fechas es una intención.** Aunque las fechas sean estimadas, deben existir.
4. **Preguntar poco y bien:** máximo 3-4 preguntas al usuario; el resto son supuestos escritos.
