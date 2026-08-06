---
name: derecho-societario-sas
description: >
  Guía operativa para constituir, gobernar y operar una SAS (Sociedad por Acciones Simplificada) en Colombia bajo la Ley 1258 de 2008. Usa esta skill SIEMPRE que el usuario mencione: SAS, sociedad, constituir empresa, estatutos, acta de constitución, Cámara de Comercio, RUT, NIT, accionistas, capital social, junta directiva, representante legal, reforma estatutaria, acuerdo de accionistas, liquidación, disolución, registro mercantil, renovación matrícula, matrícula mercantil, objeto social, Ley 1258, persona jurídica, razón social, certificado de existencia, o cuando quiera crear, modificar, transformar o cerrar una empresa en Colombia. También activar cuando se discuta la diferencia entre operar como persona natural vs. persona jurídica, o cuando un proyecto requiera evaluar si necesita constituir sociedad antes de firmar contratos (caso frecuente en Cifra).
---

# Derecho Societario SAS — Guía Operativa Colombia

## ⚠️ Regla #0: No soy abogado

Esta skill produce borradores, guías y análisis de alta calidad basados en la normativa vigente, pero **NO constituye asesoría legal**. Para la firma de documentos definitivos ante Cámara de Comercio o con terceros, recomendar siempre validación por un abogado. Dicho esto, el objetivo es que el borrador que salga de aquí necesite mínima intervención profesional.

## Cuándo leer los archivos de referencia

- **Constitución de SAS** → `references/ley-1258-sas.md` (ley completa + modelo de estatutos)
- **Trámites ante Cámara de Comercio** → `references/tramites-ccb.md`
- **Acuerdos entre cofundadores** → `references/acuerdo-accionistas.md`
- **Registro mercantil y reformas** → `references/decreto-2042.md`

Lee el archivo relevante ANTES de redactar cualquier documento. No confíes en memoria para artículos específicos.

## Árbol de decisión: ¿Qué necesita el usuario?

```
¿Qué quiere hacer?
│
├── CONSTITUIR una empresa nueva
│   ├── ¿Solo o con socios? → Define documento (acto unilateral vs. contrato)
│   ├── ¿Capital? → Mínimo legal: $0 suscrito (pero definir autorizado)
│   ├── ¿Objeto social? → Recomendación: amplio ("cualquier actividad lícita")
│   └── Genera: estatutos + formulario RUES + pre-RUT → lee ley-1258-sas.md + tramites-ccb.md
│
├── FORMALIZAR acuerdo entre cofundadores
│   ├── ¿Vesting? ¿Cláusula de salida? ¿Decisiones reservadas?
│   └── Genera: acuerdo de accionistas → lee acuerdo-accionistas.md
│
├── REFORMAR estatutos existentes
│   ├── ¿Qué cambia? (nombre, objeto, capital, representante legal)
│   └── Genera: acta de asamblea + reforma → lee decreto-2042.md
│
├── EVALUAR si necesita constituir empresa
│   ├── Criterios: ¿firma contratos con empresas grandes? ¿sector regulado?
│   ├── ¿Facturación esperada? ¿Riesgo patrimonial?
│   └── Análisis: persona natural vs. SAS → costo-beneficio
│
└── CERRAR o LIQUIDAR empresa
    ├── ¿Voluntaria o judicial?
    └── Proceso: acta de disolución → liquidación → cancelación matrícula
```

## Proceso de constitución SAS — Paso a paso

### Requisitos mínimos (Art. 5, Ley 1258/2008)

El documento de constitución debe contener:
1. Nombre, documento de identidad y domicilio de los accionistas
2. Razón social seguida de "S.A.S."
3. Domicilio principal
4. Término de duración (puede ser indefinido)
5. Objeto social (puede ser amplio: "cualquier actividad lícita")
6. Capital autorizado, suscrito y pagado + clase y número de acciones
7. Forma de administración + representante legal con facultades

### Ventajas clave de la SAS
- **Responsabilidad limitada** al monto de los aportes (Art. 1)
- **Un solo accionista** es suficiente (Art. 1)
- **Sin junta directiva obligatoria** (Art. 25)
- **Objeto social amplio** permitido (Art. 5, num. 5)
- **Constitución por documento privado** (sin escritura pública, salvo aportes de inmuebles)
- **Duración indefinida** permitida (Art. 5, num. 4)
- **Voto múltiple** y acciones con derechos diferenciados permitidos (Art. 10-11)

### Costos estimados de constitución (2026)

| Concepto | Valor aproximado |
|----------|-----------------|
| Matrícula mercantil (tarifa CCB según activos) | $0 — $200.000 COP (activos < $10M) |
| Formulario RUES | ~$6.500 COP |
| Impuesto de registro (Gobernación) | 0.7% del capital suscrito |
| PRE-RUT en DIAN (virtual) | $0 |
| Autenticación de firmas (opcional en SAS) | ~$15.000 COP por firma |
| **Total estimado para startup pequeña** | **$50.000 — $250.000 COP** |

### Checklist de constitución

- [ ] Verificar homonimia (nombre disponible) en RUES
- [ ] Redactar documento de constitución (estatutos)
- [ ] Definir capital autorizado / suscrito / pagado
- [ ] Tramitar PRE-RUT en portal DIAN
- [ ] Registrar en Cámara de Comercio (virtual o presencial)
- [ ] Obtener RUT definitivo con NIT asignado
- [ ] Abrir cuenta bancaria empresarial
- [ ] Inscribir libros de comercio (actas, accionistas)
- [ ] Registrar resolución de facturación ante DIAN (si aplica)

## Acuerdos de accionistas — Cláusulas críticas para startups

Para cofundadores de startups tech, el acuerdo de accionistas debe cubrir:

1. **Vesting** — adquisición gradual de acciones (ej. 4 años con cliff de 1 año)
2. **Dedicación mínima** — horas/semana comprometidas por cada cofundador
3. **Cláusula de salida (buy-sell)** — qué pasa si un cofundador se va
4. **Derecho de preferencia** — si alguien vende, los demás tienen prioridad
5. **Tag-along / Drag-along** — protección de minoritarios / poder de mayoría
6. **Decisiones reservadas** — qué decisiones requieren unanimidad
7. **No competencia** — restricción durante y después de la sociedad
8. **Propiedad intelectual** — cesión de IP creada por cofundadores a la sociedad
9. **Valoración** — método para valorar acciones en caso de salida

## Persona natural vs. SAS — Cuándo constituir

| Factor | Persona natural | SAS |
|--------|----------------|-----|
| **Responsabilidad** | Ilimitada (patrimonio personal) | Limitada al aporte |
| **Contratos con empresas grandes** | Aceptado a veces, pero genera desconfianza | Esperado por procurement |
| **Facturación electrónica** | Obligatoria desde ciertos umbrales | Obligatoria |
| **Costo de operación** | $0 adicional | Renovación matrícula anual + contabilidad |
| **Percepción profesional** | Freelancer | Empresa formal |
| **Recomendación para Cifra** | Solo para proyectos menores | **Constituir antes de firmar con sector financiero** |

## Formato de entregables

Cuando el usuario pida redactar estatutos, acuerdos u otros documentos societarios:
1. Siempre preguntar: ¿cuántos accionistas? ¿distribución de capital? ¿quién es representante legal?
2. Usar lenguaje formal jurídico colombiano
3. Incluir todos los elementos del Art. 5 de la Ley 1258
4. Marcar con [COMPLETAR] los campos que requieren datos específicos
5. Ofrecer generar como archivo .docx descargable
