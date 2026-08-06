# Integración con el Sistema Notion KML y con las demás Skills

Este archivo conecta esta skill con la infraestructura de gestión de proyectos
en Notion y con las otras dos skills del ecosistema (`csdd-mentor`,
`modern-dev-stack`). Un proyecto SaaS multi-tenant casi siempre cae en
Complejidad Compleja o Enterprise — por eso esta skill rara vez se activa sola.

## Taxonomía compartida de Complejidad

| Complejidad | Qué implica para un SaaS | CSDD | Skills a activar |
|---|---|---|---|
| 🟡 **Media** | SaaS muy simple, un solo tenant tipo "cliente único", sin aislamiento real todavía | CSDD ligero | `modern-dev-stack` (esta skill rara vez aplica aquí) |
| 🟠 **Compleja** | Multi-tenant real con Pool o Schema, varios clientes pagando, sin regulación estricta | CSDD completo | `saas-architect` + `csdd-mentor` + `modern-dev-stack` |
| 🔴 **Enterprise** | Multi-tenant con datos regulados, SSO/SCIM, clientes enterprise, Silo para algunos tiers | CSDD completo con Verification Gate estricto | Las tres skills juntas |

**Regla práctica:** Si el usuario describe un producto con más de un cliente
pagando o planea tenerlo, activa esta skill Y pregunta la Complejidad para
saber si además hace falta `csdd-mentor` completo (casi siempre sí, a partir
de Compleja).

## Cuándo esta skill implica activar csdd-mentor obligatoriamente

Un sistema multi-tenant maneja por definición datos de múltiples clientes en
la misma infraestructura. Eso activa automáticamente estos artículos de
`csdd-mentor` (referenciar ahí para el detalle completo):

- **Artículo III (Input Validation)** — mapea directo al middleware de
  tenant context de esta skill
- **Artículo IV (Least Privilege by Default)** — mapea a RLS y roles por tenant
- **Artículo VI (Fail Secure)** — un tenant no resuelto debe fallar cerrado
  (403), nunca pasar sin filtro — ver `references/security-isolation.md`
- **Artículo VII (Auditability)** — logs con tenant_id, igual que el
  checklist de auditoría de esta skill

No dupliques el trabajo: cuando definas el modelo de aislamiento con esta
skill, dile al usuario qué artículos de `csdd-mentor` cubre esa decisión
técnica, en vez de re-explicar seguridad desde cero.

## Sistema de gestión en Notion

Bases relevantes para proyectos SaaS (nombres exactos):

- `🚀 Proyectos` — al crear un proyecto SaaS, el campo `Tipo de proyecto`
  debe ser `💼 SaaS / Producto Digital` y `Complejidad` normalmente
  `🟠 Compleja` o `🔴 Enterprise`
- `📐 Documentos CSDD` — aquí van constitution.md, spec.md y CWE-mapping.md
  de este proyecto; el modelo de tenencia elegido debe quedar documentado
  como parte del spec.md, no solo en el chat
- `✅ Tareas` — usa `Fase CSDD` para marcar en qué etapa está cada tarea de
  arquitectura (Diagnóstico, Constitución, Spec, Implementación...)

Si el MCP de Notion está disponible, ofrece crear o actualizar la fila del
proyecto y registrar el modelo de tenencia elegido directamente, en vez de
solo describírselo al usuario.
