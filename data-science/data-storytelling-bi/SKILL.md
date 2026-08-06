---
name: data-storytelling-bi
description: Guía operativa para comunicación de datos, storytelling y diseño de dashboards de Business Intelligence. Úsala cuando el usuario necesite estructurar un informe ejecutivo (Pirámide de Minto, SCQA), diseñar o depurar un dashboard (Power BI, Tableau, Looker Studio, Metabase) siguiendo los principios de Stephen Few, Edward Tufte o Cole Nussbaumer Knaflic, o decidir cómo organizar/priorizar información visual para una audiencia directiva vs. una audiencia técnica.
---

# Comunicación de Datos: Storytelling y Business Intelligence

## Cuándo se usa esta skill
Cuando el objetivo ya no es analizar sino **comunicar** un hallazgo o construir una interfaz para que otros lo exploren.

## Distinción clave que hay que hacer siempre primero
**Visualización exploratoria** (el analista descubre patrones, sin audiencia externa) vs. **visualización explicativa** (comunicar una conclusión ya validada a alguien que debe decidir algo). Son objetivos distintos con reglas de diseño distintas — mezclar ambas es el error de origen más común.

*(Si lo que necesitas es elegir librería para explorar — Matplotlib vs Seaborn vs Plotly —, eso vive en `eda-feature-engineering`. Esta skill asume que ya sabes qué quieres comunicar y se enfoca en cómo estructurarlo y diseñarlo para una audiencia.)*

## Para informes/presentaciones ejecutivas: Consultora Estratégica (SCQA + Minto + Few)
1. **SCQA** (Situación, Complicación, Pregunta, Respuesta): abre contextualizando el problema de negocio antes de mostrar cualquier dato.
2. **Pirámide de Minto**: la respuesta/recomendación va **primero**, seguida de argumentos MECE (mutuamente excluyentes, colectivamente exhaustivos) que la sostienen — nunca construir tensión narrativa antes de la conclusión en un entorno corporativo.
3. **Stephen Few**: la interfaz que sostiene esa recomendación debe caber en una sola pantalla, sin velocímetros ni gráficos 3D decorativos; usar *bullet graphs* y *sparklines* en su lugar.

## Para dashboards interactivos de BI: Navegación Dinámica (LATCH + Shneiderman + Tufte)
1. **LATCH** (Location, Alphabet, Time, Category, Hierarchy): son las únicas 5 formas válidas de ordenar información — úsalo para decidir la estructura de menús/carpetas antes de diseñar nada visual.
2. **Mantra de Shneiderman**: *Overview first, zoom & filter, details-on-demand* — el usuario debe ver el todo antes de poder filtrar, y poder profundizar sin perder la referencia global.
3. **Edward Tufte**: maximizar el *ratio datos-tinta* (nada de adornos que no representen un valor real) y verificar el **Lie Factor** (la variación visual debe ser proporcional a la variación real de los datos, idealmente entre 0.95 y 1.05).

## Tabla de referencia rápida

| Framework | Para qué sirve | Cuándo NO usarlo |
|---|---|---|
| **Pirámide de Minto** | Estructurar la lógica de un informe/discurso | En fases de exploración sin hipótesis aún |
| **Stephen Few** | Diseño físico de dashboards directivos | Paneles exploratorios densos para analistas expertos |
| **Mantra de Shneiderman** | Interacción en BI dinámico | Presentaciones estáticas (PDF, diapositivas impresas) |
| **Storytelling con Datos (Knaflic)** | Refinar gráficos de oficina para comités | EDA técnico, gráficos de dispersión densos |
| **Edward Tufte** | Integridad y densidad visual | Comunicación de marketing masivo donde se prioriza lo decorativo |
| **Brent Dykes** | Persuasión ante comités ante decisiones de alta incertidumbre | Reportes operativos rutinarios semanales |

## Anti-patrones
- **"Viaje del héroe" en un dashboard operativo diario**: forzar una estructura narrativa dramática en una interfaz que un supervisor de planta necesita leer en 5 segundos — colapsa la eficiencia.
- **Pirámide de Minto + lienzo saturado de gráficos circulares sin contraste**: la lógica argumental es correcta pero el diseño visual la sabotea; la estructura lógica y el diseño físico deben resolverse juntos, no por separado.

## Glosario mínimo
- **MECE**: los argumentos de un mismo nivel no se solapan y cubren el 100% del alcance — base de Minto.
- **Ratio Datos-Tinta**: proporción de tinta/píxeles dedicada a representar valores reales vs. decoración.
- **Bullet graph / Sparkline**: alternativas compactas y honestas a velocímetros y gráficos de tendencia sobredimensionados.
- **Star Schema (Modelado en estrella)**: tabla de hechos (numérica) rodeada de tablas de dimensiones (descriptivas) — base del modelado en Power BI/Tableau.

## Nota de contexto
La automatización vía IA generativa (Copilot en BI, agentes conversacionales sobre el dato) está desplazando la creación manual de reportes fijos, pero **no** los principios de diseño de esta skill — un dashboard generado por un agente sigue debiendo respetar Few, Tufte y Shneiderman para ser usable.
