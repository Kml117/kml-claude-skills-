---
name: telemas-plataforma
description: |
  Skill de contexto y orquestación del proyecto PLATAFORMA INTERNA DE TELEMAS S.A.S.
  Activa esta skill SIEMPRE que la conversación sea sobre el desarrollo de la plataforma
  de operaciones de Telemas: el cotizador, la migración del CRM de Notion, el módulo de
  servicio en campo (bitácora, check-in/out de técnicos), el inventario (importaciones
  Fermax o herramienta física), la analítica de la plataforma, o su arquitectura
  (Supabase, PWA offline-first, roles). Actívala también con: "plataforma Telemas",
  "cotizador Telemas", "bitácora técnicos", "inventario Fermax", "servicios técnicos
  Telemas", "PROYECTO-TELEMAS", o cuando Marco retome el proyecto en un chat nuevo.
  NO la actives para otros proyectos de Marco (Cifra, universidad) — esos van por
  separado para evitar confusión.
metadata:
  proyecto: Telemas S.A.S. — plataforma interna de operaciones
  responsable: Marco Santoyo
  version: 0.1.0
  fuente_de_verdad: PROYECTO-TELEMAS.md
  creada: 2026-07-06
---

# 🏢 Skill de proyecto — Plataforma interna de Telemas

## Qué es esta skill (y qué NO es)

Esta es una skill de **contexto y orquestación de un proyecto específico**, no una
metodología. **No contiene doctrina CSDD** — esa vive en `csdd-mentor` y no se duplica
aquí. Su trabajo es: (1) declarar que estamos en el proyecto Telemas, (2) cargar la
fuente de verdad, (3) decir qué otras skills usar y cuándo.

## Regla #1 — Cargar la fuente de verdad primero

El estado del proyecto NO vive en la memoria del chat, vive en **`PROYECTO-TELEMAS.md`**.
Al iniciar cualquier sesión de este proyecto:

1. Pide o localiza `PROYECTO-TELEMAS.md`.
2. Léelo completo antes de responder.
3. Continúa desde el "Tablero de tareas" y las "Preguntas abiertas".
4. Al cerrar la sesión, actualiza ese documento (tareas, decisiones, requisitos nuevos).

Si el usuario abre un chat nuevo porque el anterior se llenó, esto es lo que garantiza
cero pérdida de contexto.

## Resumen del proyecto (referencia rápida — la verdad está en el doc maestro)

- **Qué es:** plataforma interna (FSM + CRM + ERP ligero), NO un SaaS multi-tenant.
- **Módulos:** cotizador (ventas), CRM (migrar de Notion), servicio en campo (técnicos),
  inventario (Fermax + herramienta), analítica.
- **Roles:** administración (todo), ventas (cotizador + clientes), técnico (solo sus
  servicios + bitácora + herramienta).
- **Arquitectura:** monolito modular · Supabase (Postgres + RLS) · PWA offline-first ·
  Vercel/Netlify.
- **Módulo de arranque:** servicio en campo.

## Cómo coordinar con otras skills

| Necesidad | Skill a usar |
|---|---|
| Constitución de seguridad, specs, matriz de trazabilidad | `csdd-mentor` (Modo A, Complejidad Compleja) |
| Stack, arquitectura, setup técnico | `modern-dev-stack` |
| Analítica, EDA, modelos, pronóstico de importaciones | `data-science-mentor` y las skills de ML/EDA |
| Cumplimiento Ley 1581 (datos de clientes + geolocalización de técnicos) | `data-ethics-governance` |
| Multi-tenancy — SOLO si algún día se vende a otras empresas | `saas-architect` |

## Sensibilidades permanentes

- **Ley 1581/2012:** la geolocalización de técnicos es dato sensible. Consentimiento,
  minimización de retención y transparencia son requisito, no opción.
- **Idioma:** español. Artefactos en Markdown compatible con Notion.
- **Framing de marca:** Marco es "AI-Assisted Builder". Mantener honestidad.
- **Alcance:** el enemigo del proyecto es querer construir todo de una vez. Un módulo a la
  vez, sobre el mismo cimiento. Rechaza el big-bang.

## Antipatrón a evitar

No mezclar este proyecto con Cifra ni con los proyectos universitarios de Marco. Si la
consulta no es sobre la plataforma de Telemas, esta skill no aplica.
