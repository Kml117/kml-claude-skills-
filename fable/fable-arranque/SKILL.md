---
name: fable-arranque
description: Arranque de proyecto estilo Fable 5. Usar esta skill SIEMPRE que el usuario esté iniciando algo nuevo — un proyecto legal, técnico, comunitario, académico, agrícola o de datos — para asegurar que arranque con la estructura correcta antes de producir contenido. Activar cuando el usuario diga "quiero empezar", "cómo debería estructurar esto", "primeros pasos", "tengo una idea", o presente una meta nueva sin plan previo, aunque no pida explícitamente ayuda para arrancar. También activar cuando el usuario pida directamente un entregable grande (documento, app, programa) sin haber definido objetivo, destinatario ni criterio de terminado.
---

# Fable Arranque — Empezar bien es la mitad del trabajo

Este manual codifica cómo Fable 5 inicia un proyecto. El principio central: **resistir la tentación de producir antes de entender.** El error más costoso en cualquier proyecto es construir rápido la cosa equivocada.

## Frontera con fable-plan (para no duplicar diagnóstico)

- **Arranque** = el proyecto aún no tiene forma: falta objetivo, destinatario o Definición de Terminado. Se activa esta skill.
- **Plan** = el objetivo ya está claro y verificable; lo que falta es la ruta. Se activa `fable-plan` directamente, sin repetir la Fase 0 de aquí.
- Si arranque ya corrió en esta conversación, `fable-plan` hereda sus respuestas — nunca preguntar dos veces lo mismo.

## Traspaso a skills especializadas (evitar doble diagnóstico)

Si en la Fase 0 se revela que el proyecto es de un dominio que ya tiene su propio flujo de diagnóstico, esta skill hace SOLO las 4 preguntas universales y luego traspasa — no duplica el diagnóstico técnico:

| El proyecto es... | Traspasar a |
|---|---|
| Software (web, app, API) | `modern-dev-stack` (+ prompt de inicio según Complejidad en Notion) |
| Datos / ML | `data-science-mentor` |
| Cliente de Cifra | `cifra-playbook` (que ya secuencia todo lo demás) |
| SaaS multi-tenant | `saas-architect` + `csdd-mentor` |
| Legal, comunitario, académico, agrícola, familiar | Esta skill lo lleva completo — no hay especializada |

## Fase 0 — Diagnóstico (obligatorio, antes de escribir una sola línea de contenido)

Cuatro preguntas. Si el usuario ya respondió alguna en la conversación, no la repitas — extrae la respuesta y confírmala. Si faltan, pregunta las que falten (máximo las esenciales, no un interrogatorio):

1. **¿Cuál es el objetivo real?** No la tarea superficial ("redactar una carta") sino el resultado buscado ("que la entidad responda dentro del término legal"). La tarea es un medio; diseña para el fin.
2. **¿Quién es el destinatario final y qué espera?** Un juez, un banco, una junta comunal y un desarrollador esperan formatos, tonos y niveles de detalle completamente distintos.
3. **¿Qué restricciones son innegociables?** Plazos (términos procesales, fechas de convocatoria), normativa aplicable, presupuesto, herramientas disponibles.
4. **¿Cuál es la Definición de Terminado?** La prueba concreta de que está completo: "el documento está radicado", "la app corre sin errores con datos reales", "el comité aprobó el reglamento". Si no se puede verificar, no está definido.

## Fase 1 — Supuestos explícitos

Lista todo lo que se está asumiendo sin confirmación: "asumo que el plazo es X", "asumo que ya existe el documento Y", "asumo que el destinatario acepta formato digital". Marca cada supuesto como **[VALIDAR]**. Un supuesto no validado que resulta falso a mitad del proyecto cuesta el triple que validarlo al inicio.

## Fase 2 — Arquitectura antes que contenido

Define el esqueleto completo antes de redactar o programar:
- **Documento:** índice de secciones con una línea describiendo qué logra cada una.
- **Código:** módulos/componentes y qué responsabilidad tiene cada uno.
- **Proceso/trámite:** secuencia de pasos con responsable y evidencia de cada paso.

Presenta el esqueleto al usuario para aprobación **antes** de desarrollarlo. Corregir un esqueleto toma minutos; corregir un desarrollo completo toma horas.

## Fase 3 — Plan mínimo viable

Identifica la secuencia más corta de pasos que produce algo **evaluable** — no algo terminado, algo que ya se puede juzgar. Ejemplos: la sección más crítica del documento, el flujo principal de la app, el primer trámite de la cadena. Producir eso primero permite corregir el rumbo temprano.

## Checklist final de arranque

Antes de pasar a ejecución, confirma que existe:
- [ ] Objetivo real formulado en una oración.
- [ ] Destinatario y formato esperado identificados.
- [ ] Restricciones y plazos anotados (con fechas concretas, no "pronto").
- [ ] Definición de Terminado verificable.
- [ ] Lista de supuestos con marcas [VALIDAR].
- [ ] Esqueleto aprobado por el usuario.
- [ ] Primer entregable evaluable definido.
- [ ] Punto de control intermedio: cuándo y con qué criterio se revisará el avance.

## Reglas de oro

1. **Nunca preguntar lo que la conversación ya respondió.** Extraer primero, preguntar después.
2. **Máximo una ronda de preguntas.** Concentra lo esencial; el resto se marca como supuesto [VALIDAR].
3. **Si el usuario insiste en saltar el diagnóstico, obedecer — pero dejar los supuestos escritos** al inicio del entregable, para que el riesgo quede visible.
4. **El arranque termina cuando hay esqueleto aprobado,** no cuando hay entusiasmo compartido.

## El ciclo Fable completo

Esta skill es la puerta de entrada de una familia de 5:

```
fable-arranque → fable-plan → (ejecución) → fable-dice (si algo falla)
                                          → fable-opinion (revisión de lo producido)
                                          → fable-seguridad (antes de entregar)
```

No activarlas todas de golpe — cada una entra en su momento del ciclo.
