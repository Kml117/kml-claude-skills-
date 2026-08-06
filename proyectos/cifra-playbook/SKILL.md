---
name: cifra-playbook
description: Orquestador para cualquier engagement de Cifra (agencia de Data Science as a Service co-fundada con Marco). Actívala cuando el usuario mencione Cifra, un prospecto o cliente de Cifra (ej. bancos, aseguradoras, e-commerce), una propuesta comercial de Cifra, o el ciclo de vida de un engagement desde calificación hasta entrega. Secuencia cuándo activar startup-architect (pricing/modelo de negocio), saas-architect (arquitectura multi-tenant), data-science-mentor (entrega técnica), csdd-mentor + data-ethics-governance (compliance internacional/frameworks), el bloque legal colombiano (derecho-societario-sas, contratos-tech-colombia, proteccion-datos-operativa, propiedad-intelectual-software, tributario-startup-col, laboral-contratistas-col), y social (comunicación/caso de estudio). No reemplaza ninguna de esas skills — decide el orden y cuáles aplican.
---

# Cifra Playbook — Orquestador de Engagements

## Contexto de Cifra
- Agencia de Data Science as a Service (DSaaS), co-fundada con Marco.
- 3 planes: $1,000 / $1,500 / $2,000 USD/mes (plan Data destacado "Más solicitado").
- Landing de ventas: `cifra-landing.html`. Posicionamiento: "Contrata la entrega, no el puesto".
- El ecosistema `Cifra` ya existe como opción en la base de Notion `💻 Trazabilidad de Software`.

## El dato que cambia el enfoque por defecto

Las conversaciones de venta activas conocidas son con bancos, un asegurador y un gigante de e-commerce. Eso no es casualidad — es el perfil natural de quién necesita y puede pagar DSaaS a esta escala de precio. **Por eso el supuesto por defecto de cualquier engagement de Cifra debe ser conservador, no evaluado caso por caso desde cero:**

- **Complejidad = 🟠 Compleja como piso**, no como techo. Sube a 🔴 Enterprise en cuanto haya datos financieros o personales reales de punta a punta.
- **`data-ethics-governance` + `proteccion-datos-operativa` se activan por defecto** al calificar el prospecto, no "si acaso surge". La primera cubre el framework internacional; la segunda redacta el documento colombiano real (contrato de encargado bajo Ley 1581) que se firma con el cliente.
- **El tamaño del contrato NO correlaciona con la sensibilidad del dato** — un banco pagando el plan de $1,000 sigue siendo un banco con datos regulados. No relajes el rigor por el ticket de entrada.

## Secuencia del engagement

```
1. CALIFICACIÓN COMERCIAL           → startup-architect
   ¿el pricing encaja? ¿hay fit real de unit economics con este prospecto?
   ¿Cliente del sector financiero/regulado? → evaluar aquí mismo si toca
   resolver derecho-societario-sas (constituir SAS) antes de avanzar
                    │
                    ▼
2. DIAGNÓSTICO TÉCNICO              → data-science-mentor
   Enruta a la combinación correcta de las 13 skills de DS según lo
   que el cliente pide (¿es forecasting? ¿NLP? ¿un dashboard ejecutivo?)
                    │
                    ▼
3. GATE DE COMPLIANCE (NO SALTABLE) → csdd-mentor + data-ethics-governance
                                     + proteccion-datos-operativa
                                     + contratos-tech-colombia
                                     + propiedad-intelectual-software
   Ver checklist bloqueante abajo — esto va ANTES de tocar un dato real
                    │
                    ▼
4. ARQUITECTURA DE ENTREGA          → saas-architect
   ¿Es un análisis puntual (aislamiento tipo Silo por proyecto) o un
   servicio recurrente multi-cliente (aislamiento serio, RLS, etc.)?
                    │
                    ▼
5. EJECUCIÓN TÉCNICA                → las skills de DS indicadas en el paso 2
   ¿Se subcontrata a alguien para este proyecto? → laboral-contratistas-col
                    │
                    ▼
6. ENTREGA Y CASO DE ESTUDIO        → social
   LinkedIn, caso de estudio para marca personal + insumo de la siguiente venta
                    │
                    ▼
7. POST-ENGAGEMENT (si aplica)      → tributario-startup-col
   Facturación del pago recibido, régimen tributario, documento soporte
   si se subcontrató a un freelancer no obligado a facturar
```

No hace falta pasar por todos los pasos en cada conversación — si ya calificaste al cliente en una sesión anterior, entra directo al paso que corresponda. Pero nunca te saltes el paso 3 solo porque el cliente tiene prisa.

## Checklist de compliance antes de firmar (bloqueante)

```
□ Contrato de Encargado de Datos (Ley 1581) redactado con
  proteccion-datos-operativa — sigue necesitando revisión legal humana
  antes de firmar con un cliente regulado, esta skill no la reemplaza
□ Contrato de servicios / desarrollo redactado o adaptado con
  contratos-tech-colombia, con cláusula de cesión de IP resuelta vía
  propiedad-intelectual-software (¿de quién es el código al pagar?)
□ Decisión tomada: ¿constituir SAS antes de firmar este cliente
  específico? → derecho-societario-sas (no asumas que ya se resolvió
  solo porque existe la skill; sigue siendo un OKR personal pendiente)
□ Clasificación de riesgo bajo EU AI Act si el cliente opera en UE
  o si el modelo entregado aplica a ese marco
□ Fila creada en 💻 Trazabilidad de Software: Ecosistema=Cifra,
  Tipo de desarrollo=📊 Proyecto de Datos / ML, Complejidad calibrada
  según la regla de arriba (piso Compleja)
```

Si alguno de estos está sin resolver, dilo explícitamente antes de avanzar al diseño técnico — no lo dejes como nota al pie. Producir el borrador con la skill correspondiente no cierra el punto: sigue pendiente hasta que un abogado lo valide para clientes regulados (bancos, aseguradoras).

## Registro en Notion

Cada cliente de Cifra es una fila en `💻 Trazabilidad de Software` con `Ecosistema = Cifra`. Sus tareas usan `Fase ML` para trackear la etapa del pipeline de datos, y `📐 Documentos CSDD` para `constitution.md`/`spec.md` — dado el piso de Complejidad establecido arriba, esto aplica casi siempre en Cifra, no es la excepción.

## Integración con el resto del ecosistema

- **`startup-architect`**: califica el prospecto antes de comprometer tiempo técnico — no todo lead vale la pena al pricing actual.
- **`data-science-mentor`**: hace el diagnóstico técnico real y decide qué combinación de las 13 skills de DS aplica al pedido del cliente.
- **`csdd-mentor`** + **`data-ethics-governance`**: dado el perfil de clientes, esto no es opcional — ver la tabla de mapeo hallazgo→artículo constitucional en `data-ethics-governance`. Cubren el framework internacional (fairness, explicabilidad, EU AI Act); no reemplazan el trabajo legal colombiano de abajo.
- **`saas-architect`**: si el engagement se repite o escala a servicio recurrente para varios clientes simultáneos, no solo un entregable puntual.
- **`social`**: convierte cada entrega exitosa en contenido — el caso de estudio de un cliente ayuda a cerrar al siguiente.

**Bloque legal colombiano** (nuevo — instalar antes de operar con clientes reales):
- **`derecho-societario-sas`**: resuelve la decisión de constituir SAS antes de firmar con un cliente regulado. Se consulta en el paso 1 (calificación), no solo como nota pendiente.
- **`contratos-tech-colombia`**: redacta y adapta el contrato de servicios/desarrollo para cada cliente. Es la skill que ajustó el contrato y la propuesta de Security Global.
- **`proteccion-datos-operativa`**: redacta el Contrato de Encargado de Datos bajo Ley 1581 — el documento operativo real que antes quedaba como "necesita revisión legal" sin avanzar. Sigue necesitando esa revisión, pero ahora el borrador de partida es mucho más sólido.
- **`propiedad-intelectual-software`**: resuelve la cláusula de cesión de IP en cada contrato — quién es dueño del código que Cifra entrega.
- **`tributario-startup-col`**: se activa después de constituir la SAS o al facturar un pago de cliente — RUT, régimen, facturación electrónica.
- **`laboral-contratistas-col`**: se activa si Cifra subcontrata a alguien (otro data scientist, un developer) para cumplir un proyecto de cliente.

Estas 6 skills producen borradores de alta calidad pero **no reemplazan revisión legal profesional** para contratos de alto valor o con entidades reguladas — eso sigue siendo cierto incluso después de instalarlas.

## Anti-patrón específico de Cifra

Tratar un engagement nuevo como "solo es un análisis de datos, no necesita todo esto" porque el ticket de entrada es el plan más barato. Ya se explicó arriba por qué esa lógica falla — no la repitas por presión de tiempo o de cliente.
