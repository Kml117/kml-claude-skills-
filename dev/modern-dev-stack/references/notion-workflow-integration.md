# Integración con el Sistema Notion KML y con las demás Skills

Este archivo conecta esta skill con la infraestructura de gestión de proyectos
en Notion y con las otras dos skills del ecosistema (`csdd-mentor`,
`saas-architect`). Las tres comparten la misma taxonomía de complejidad para
que no haya contradicciones entre ellas.

## Taxonomía compartida de Complejidad

Esta es la misma escala usada en el campo `Complejidad` de la base
`🚀 Proyectos` en Notion. Úsala para decidir cuánto proceso aplicar:

| Complejidad | Qué implica técnicamente | Metodología | Skills a activar |
|---|---|---|---|
| 🟢 **Simple** | Landing page, ajuste de config, feature de una sola pantalla, contenido estático | Sin CSDD — diagnóstico + implementación directa | Solo `modern-dev-stack` |
| 🟡 **Media** | Backend simple, una integración externa, auth básico, lógica de negocio moderada | CSDD ligero (solo spec.md si se justifica) | `modern-dev-stack` (+ `csdd-mentor` si el diagnóstico revela sensibilidad de datos) |
| 🟠 **Compleja** | Multi-feature, múltiples integraciones, posible multi-tenancy, va a producción con usuarios reales | CSDD completo | `modern-dev-stack` + `csdd-mentor` (+ `saas-architect` si es multi-tenant) |
| 🔴 **Enterprise** | Producto comercial con clientes pagando, compliance real, datos sensibles a escala | CSDD completo con Verification Gate estricto | Las tres skills juntas |

**Regla de oro:** Si el usuario no declara la complejidad al inicio del chat,
pregúntala explícitamente antes de recomendar stack — determina si vale la
pena invocar `csdd-mentor` o si eso sería sobre-ingeniería para el caso.

## Cuándo NO forzar CSDD

Para complejidad Simple y Media, NO actives el ritual completo de
`csdd-mentor` (constitution.md con 2-3 sesiones, Verification Gates, etc.).
Eso es sobre-ingeniería para un ajuste de configuración o una landing page.
En su lugar:
- Simple → diagnóstico de 5 puntos (Sección 1 del SKILL.md) + implementación
- Media → diagnóstico + spec.md ligero, sin constitution.md completa,
  salvo que durante el diagnóstico detectes datos sensibles o regulación
  aplicable — en ese caso, avisa al usuario y sugiere activar `csdd-mentor`

## Sistema de gestión en Notion

El usuario mantiene una infraestructura de Notion con estas bases (nombres
exactos, úsalos si el usuario pide loggear algo):

- `🚀 Proyectos` — hub central, un proyecto = una fila
- `📐 Documentos CSDD` — constitution.md, spec.md, CWE-mapping.md, etc.
- `✅ Tareas` — con sistema de doble prioridad Urgencia × Dificultad
- `🐛 Bugs & Issues` — reportes estructurados
- `🗓️ Sprints / Ciclos` — agrupación temporal de tareas
- `📝 Bitácora de Sesiones` — registro de sesiones de chat/Claude Code
- `🧰 Stack Tecnológico` — catálogo de herramientas (relación M:N con Proyectos)

Si el usuario menciona un proyecto por nombre y el MCP de Notion está
disponible en la conversación, puedes ofrecer buscar la página del proyecto
(`notion-search` o `notion-fetch`) para recuperar contexto en vez de pedirle
que te lo repita. Si el usuario pide explícitamente registrar una tarea, un
bug o una sesión, ofrece crear la página directamente con las herramientas de
Notion en lugar de solo describir qué debería anotar.

## Checklist de proyecto — versión con complejidad

Al iniciar cualquier proyecto nuevo, antes de tocar el checklist técnico del
SKILL.md principal, confirma:

```
□ ¿Cuál es la Complejidad? (Simple/Media/Compleja/Enterprise)
□ ¿Existe ya la fila del proyecto en 🚀 Proyectos?
□ ¿Hay una entrada previa en 📝 Bitácora de Sesiones que dé contexto?
□ Según la Complejidad, ¿corresponde activar csdd-mentor y/o saas-architect?
```
