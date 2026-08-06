---
name: fable-dice
description: Depuración con verificación empírica estilo Fable 5 — "el que arregla todo". Usar esta skill SIEMPRE que haya un bug, error o falla que persiste, especialmente cuando otro modelo, herramienta o persona ya afirmó que estaba resuelto y el problema sigue apareciendo. Fuerza a reproducir el error antes de tocarlo y a verificar con evidencia antes de declararlo arreglado. Activar cuando el usuario diga "esto sigue fallando", "ya lo arreglaron pero sigue el error", "el otro modelo dijo que estaba corregido", "depura esto", "por qué no funciona", o presente cualquier error recurrente.
---

# Fable Dice — El que arregla todo (verificando)

Este manual codifica cómo Fable 5 depura. El principio central: **un arreglo no verificado es una hipótesis, no un arreglo.** La razón por la que "otros modelos dicen que ya está corregido" y el error persiste es casi siempre la misma: se declaró la victoria por inferencia ("este cambio debería resolverlo") en lugar de por evidencia ("reproduje el escenario y ya no falla").

## Regla cero

**Nunca declarar algo arreglado sin (a) haber reproducido el error primero y (b) haber confirmado después, con el mismo escenario, que ya no ocurre.** Sin reproducción no hay diagnóstico; sin re-verificación no hay arreglo. Solo hay esperanza.

## Protocolo de depuración

### Paso 1 — Reproducir antes de tocar
- Obtener el error exacto: mensaje literal, pantallazo, pasos que lo disparan, con qué datos, en qué entorno.
- Reproducirlo de forma consistente. Si es intermitente, encontrar las condiciones que lo hacen aparecer.
- **Si no se puede reproducir, decirlo explícitamente** y pedir más información — nunca "arreglar" a ciegas algo que no se ha visto fallar.

### Paso 2 — Desconfiar de los arreglos anteriores
Cuando alguien (humano o modelo) dice "ya lo corregí":
- Verificar que el cambio realmente se aplicó (¿se guardó el archivo? ¿se desplegó la versión nueva? ¿se limpió la caché? ¿se reinició el servicio?). Una fracción enorme de "arreglos que no funcionan" son arreglos que nunca llegaron a ejecutarse.
- Verificar que el cambio ataca el error que se está viendo, y no un error parecido.
- Leer el arreglo con ojos frescos: ¿la lógica realmente cubre el caso que falla?

### Paso 3 — Aislar variables
- Cambiar **una cosa a la vez** y observar el efecto. Cambiar tres cosas y que funcione deja sin saber cuál era el problema — y sin saber qué se rompió de paso.
- Reducir el caso al mínimo que sigue fallando: quitar todo lo que no sea necesario para que el error aparezca. El caso mínimo casi siempre señala la causa.

### Paso 4 — Causa raíz, no síntoma
- Preguntar "¿por qué?" al menos 3 veces. "Falla el guardado" → ¿por qué? "el campo llega vacío" → ¿por qué? "el formulario no valida" → ¿por qué? "la validación se salta en móvil". El arreglo va en el último porqué, no en el primero.
- Parchar el síntoma (un `try/catch` que silencia, un `if` que esquiva el caso) sin entender la causa garantiza que el error reaparecerá con otra cara.

### Paso 5 — Verificar el arreglo con el escenario original
- Repetir **exactamente** los pasos que producían el error en el Paso 1. No un escenario parecido: el mismo.
- Si el error era intermitente, repetir suficientes veces para tener confianza real, no una sola corrida afortunada.

### Paso 6 — Chequeo de regresión
- ¿Qué más toca el código o proceso que se cambió? Probar los caminos vecinos: lo que funcionaba antes debe seguir funcionando.
- Si existe una suite de pruebas, correrla completa. Si no existe, probar manualmente al menos los 2-3 flujos principales.

### Paso 7 — Documentar con evidencia
El reporte final tiene tres partes obligatorias:
1. **Causa raíz encontrada** (una oración concreta).
2. **Cambio realizado** (qué se modificó, dónde).
3. **Cómo se verificó** (los pasos reproducidos y su resultado — evidencia, no promesa).

Si el bug pertenece a un desarrollo registrado en el sistema de Notion del usuario (workspace "Development"): ofrecer registrarlo en `✅ Tareas` con `Tipo de tarea` = `🐛 Bug`, su `Severidad`, y la causa raíz en `Notas técnicas` — los bugs no tienen base separada, son tareas con ese tag. Si el usuario quiere repasar el proceso para aprenderlo (no solo que se ejecute), la versión pedagógica vive en su nota "Testing — Cómo Depurar un Bug" de la Biblioteca de Software en Notion.

## Frases prohibidas

- "Esto ya debería estar arreglado."
- "Con este cambio debería funcionar."
- "Probablemente era eso."

Todas se reemplazan por: "Reproduje [escenario], apliqué [cambio], repetí [escenario] y el resultado fue [evidencia]."

## Regla de honestidad final

Si después del protocolo el error persiste o la causa no se encontró, **decirlo con claridad**: qué se descartó, qué hipótesis quedan y cuál es el siguiente experimento. Un "no lo encontré todavía, pero descarté A, B y C" honesto vale infinitamente más que un falso "arreglado".
