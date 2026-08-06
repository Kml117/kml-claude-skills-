# kml-claude-skills

Biblioteca personal de skills para Claude Code. Fuente única de verdad —
espejo del repo de skills correspondiente en el ecosistema KML.

## Cómo usar este repo

Este repo NO se coloca directamente en `~/.claude/skills/` (esa carpeta debe
quedar plana, sin subcarpetas de categoría — Claude Code solo escanea un
nivel). En vez de eso, clona este repo en cualquier ruta y crea un symlink
por cada skill dentro de `~/.claude/skills/`:

```bash
git clone <url-de-este-repo> ~/kml-claude-skills
mkdir -p ~/.claude/skills

# Repetir por cada skill que quieras activa (ver lista abajo)
ln -s ~/kml-claude-skills/legal/contratos-tech-colombia ~/.claude/skills/contratos-tech-colombia
ln -s ~/kml-claude-skills/dev/csdd-mentor ~/.claude/skills/csdd-mentor
# ... etc
```

Para actualizar una skill: edita el archivo dentro de este repo y haz commit.
El symlink apunta al mismo archivo, así que Claude Code ve el cambio de
inmediato sin tocar `~/.claude/skills/`.

## Índice — 42 skills en 6 categorías

### legal/ (6)
- derecho-societario-sas
- contratos-tech-colombia
- proteccion-datos-operativa
- propiedad-intelectual-software
- tributario-startup-col
- laboral-contratistas-col

### data-science/ (15)
- data-science-mentor
- data-engineering-pipelines
- eda-feature-engineering
- ml-supervised-forecasting
- ml-unsupervised-reinforcement
- computer-vision-pipelines
- nlp-llm-systems
- deep-learning-foundations
- model-evaluation-causality
- mlops-production
- data-cleaning-quality
- data-ethics-governance
- data-storytelling-bi
- data-science-foundations
- programmatic-eda

### dev/ (4)
- saas-architect
- modern-dev-stack
- csdd-mentor
- startup-architect

### fable/ (5) — metodología Fable 5
- fable-plan
- fable-arranque
- fable-dice
- fable-opinion
- fable-seguridad

### proyectos/ (3) — orquestadores específicos
- cifra-playbook
- convocatorias-colombia
- telemas-plataforma

### general/ (9)
- cybersecurity
- ckmdesign-system
- ckmslides
- ckmui-styling
- ui-ux-pro-max
- research
- social
- math-mentor-ds
- preparcial-matematicas

## Skills prioritarias para proyectos de código (referencia rápida)

Para cualquier proyecto bajo CSDD (Cifra, Geoparque, Telemas), las skills
mínimas recomendadas en `~/.claude/skills/`:
- `csdd-mentor` — auditoría de constitution.md / spec.md
- `cybersecurity` — auditoría de 8 dimensiones antes de merge
- `fable-seguridad` — chequeo antes de publicar/exponer
- `fable-dice` — depuración con verificación empírica

## Nota sobre portabilidad de proyectos

Los symlinks de este repo solo funcionan en la máquina donde se crearon.
Si un proyecto (ej. `landing-geoparque-zaquenzipa`) necesita que sus skills
viajen con el código para que otra persona (ej. Marco) las tenga al clonar,
esas skills específicas se copian físicamente dentro de
`<proyecto>/.claude/skills/` — no se enlazan. Ver traceability-matrix.md
de cada proyecto para la versión (hash de commit) de la skill copiada.
