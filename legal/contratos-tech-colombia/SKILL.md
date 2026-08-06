---
name: contratos-tech-colombia
description: >  Redacta, revisa y negocia contratos de servicios tecnológicos en Colombia: desarrollo de software, consultoría de datos/IA, NDAs, SLAs, términos de uso, soporte y confidencialidad. Usa SIEMPRE que el usuario mencione: contrato de servicios, contrato de desarrollo, NDA, acuerdo de confidencialidad, términos y condiciones, SLA, contrato de consultoría, contrato de soporte, cláusula de penalidad, garantía de software, contrato con cliente, propuesta comercial, anticipo, cesión de derechos, cláusula de propiedad intelectual, contrato de mantenimiento, resolución de conflictos, cláusula arbitral, contrato de suscripción, prestación de servicios, o cuando necesite redactar o negociar cualquier contrato comercial para proyectos de tecnología en Colombia. También para diferencias entre contrato civil y comercial, o validez de contratos electrónicos (Ley 527/1999).
---

# Contratos de Servicios Tecnológicos — Colombia

## ⚠️ Regla #0: No soy abogado

Esta skill produce borradores contractuales de alta calidad basados en el marco legal colombiano, pero NO constituye asesoría legal. Para contratos de alto valor o con entidades reguladas, recomendar validación profesional.

## Cuándo leer los archivos de referencia

- **Marco legal de obligaciones y contratos** → `references/codigo-civil-obligaciones.md`
- **Contratos mercantiles** → `references/codigo-comercio-contratos.md`
- **Firma y documentos electrónicos** → `references/ley-527-comercio-electronico.md`
- **Cláusulas modelo y patrones** → `references/modelos-clausulas.md`
- **Frameworks internacionales, anti-patrones y glosario** → `references/frameworks-estandares-internacionales.md`

Lee el archivo relevante ANTES de redactar cláusulas específicas. Para contratos B2B SaaS o MSAs, lee el archivo de frameworks PRIMERO — contiene las combinaciones ganadoras (Common Paper + WCC + DPA Ley 1581) y los anti-patrones a evitar.

## Diagnóstico inicial — Preguntar SIEMPRE

Antes de redactar cualquier contrato:
1. **¿Quién contrata a quién?** — ¿Cifra presta el servicio o lo recibe? ¿El cliente es persona natural o jurídica?
2. **¿Qué tipo de servicio?** — desarrollo de software, consultoría, ciencia de datos, soporte, SaaS
3. **¿Cuál es el modelo de pago?** — por proyecto (hitos), suscripción mensual, por hora, retainer
4. **¿Qué pasa con la propiedad intelectual?** — ¿el código es del cliente al pagar? ¿licencia? ¿Cifra retiene derechos de caso de estudio?
5. **¿Hay datos personales involucrados?** — si sí, activar `proteccion-datos-operativa`

## Tipos de contrato y cuándo usar cada uno

| Tipo | Cuándo usarlo | Cláusulas clave |
|------|---------------|-----------------|
| **Prestación de servicios** | Proyecto a medida con entregables definidos | Alcance, hitos, IP, garantía, cambios de alcance |
| **Contrato de suscripción** | Servicio recurrente (DSaaS mensual) | Duración, renovación, SLA, cancelación, ajuste de precio |
| **NDA / Confidencialidad** | Antes de compartir información sensible | Definición de confidencial, duración, excepciones, remedio |
| **SLA** | Compromisos de disponibilidad y respuesta | Uptime %, tiempos de respuesta, penalidades, exclusiones |
| **Contrato de soporte** | Post-entrega, mantenimiento correctivo/evolutivo | Alcance del soporte, horas incluidas, excedentes, vigencia |
| **Términos de uso** | Plataforma web/SaaS con usuarios finales | Uso aceptable, limitación de responsabilidad, datos, jurisdicción |
| **Adenda / Orden de servicio** | Nuevo proyecto con cliente existente | Referencia al contrato marco, alcance específico, precio |

## Estructura estándar de un contrato de servicios tech

1. **Encabezado** — partes, identificación, representación legal
2. **Considerandos** — contexto, antecedentes, por qué se celebra
3. **Objeto** — qué servicio se presta, referencia a anexo técnico
4. **Alcance y exclusiones** — qué incluye y qué NO incluye
5. **Plazo y vigencia** — duración, renovación, terminación anticipada
6. **Precio y forma de pago** — valor, hitos, anticipos, moneda, IVA
7. **Obligaciones del prestador** — entregables, calidad, confidencialidad
8. **Obligaciones del cliente** — información, accesos, aprobaciones, pagos
9. **Propiedad intelectual** — quién es dueño del código, cesión, licencia, caso de estudio
10. **Garantía** — período, alcance (bugs vs. nuevas funcionalidades), exclusiones
11. **Confidencialidad** — definición, duración (típico: 2-5 años post-terminación)
12. **Protección de datos** — referencia a contrato de encargado (Ley 1581)
13. **Responsabilidad y limitaciones** — tope de responsabilidad, exclusión de daños indirectos
14. **Cambios de alcance** — procedimiento para solicitar, cotizar y aprobar cambios
15. **Terminación** — causales, preaviso, efectos (entrega de avance, pagos pendientes)
16. **Resolución de controversias** — negociación directa → mediación → juez ordinario (o arbitraje)
17. **Ley aplicable** — leyes de Colombia
18. **Firmas** — nombre, cédula/NIT, cargo, fecha

## Cláusulas críticas para Cifra

### Propiedad intelectual — Patrón recomendado

```
Cesión al cliente del código específico del proyecto una vez pagado el 100%.
Cifra retiene:
- Herramientas, frameworks y librerías propias preexistentes (licencia de uso al cliente)
- Conocimiento general y know-how
- Derecho de usar el proyecto como caso de estudio (sin datos sensibles)
```

### Cambios de alcance — Protección contra scope creep

```
Cualquier funcionalidad no descrita en el Anexo Técnico constituye un cambio de alcance.
Cambios se cotizarán por separado, requieren aprobación escrita del cliente,
y pueden ajustar el cronograma y el precio del proyecto.
```

### Limitación de responsabilidad

```
La responsabilidad total acumulada del prestador no excederá
el valor total del contrato (o del último período de 12 meses en contratos recurrentes).
Se excluyen daños indirectos, lucro cesante, pérdida de datos
no causada por negligencia del prestador.
```

### Garantía de software — Redacción segura

```
[60/90] días contra defectos de software (bugs que impidan el funcionamiento
conforme a las especificaciones del Anexo Técnico).
NO cubre: nuevas funcionalidades, cambios de alcance, errores causados por
modificaciones del cliente, problemas de infraestructura de terceros.
```

## Contrato civil vs. comercial — Cuándo aplica cada régimen

| Criterio | Civil | Comercial |
|----------|-------|-----------|
| **Partes** | Al menos una es no comerciante | Ambas son comerciantes (inscritas en registro mercantil) |
| **Norma base** | Código Civil (Art. 1495+) | Código de Comercio (Art. 864+) |
| **Solidaridad** | No se presume | Se presume en obligaciones mercantiles |
| **Prescripción** | 10 años (ordinaria) | 5 años (acciones de comercio) |
| **Cifra típicamente** | Si contrata con persona natural no comerciante | Si contrata con SAS, S.A., o comerciante registrado |

## Validez de contratos electrónicos (Ley 527/1999)

La Ley 527 equipara el documento electrónico al documento escrito, y la firma electrónica a la firma manuscrita, siempre que:
- Se pueda identificar a la persona que firma
- El método sea confiable y apropiado para el propósito
- El mensaje de datos sea accesible para consulta posterior

**Implicación práctica:** los contratos de Cifra firmados por correo electrónico con aceptación explícita, o mediante plataformas de firma electrónica (AutenTIC, DocuSign), tienen plena validez legal en Colombia.

## Formato de entregables

Cuando el usuario pida redactar un contrato:
1. Preguntar las 5 preguntas del diagnóstico inicial
2. Elegir el tipo de contrato apropiado
3. Usar la estructura estándar como esqueleto
4. Incluir las cláusulas críticas para Cifra cuando aplique
5. Marcar con [COMPLETAR] los campos que requieren datos específicos
6. Ofrecer generar como archivo .docx descargable
7. Si hay datos personales, mencionar que se necesita contrato de encargado (`proteccion-datos-operativa`)

## Anti-patrones — Verificar siempre antes de entregar

### "Copy-Paste Transfronterizo"
Importar contratos de Common Law sin adaptación. Conceptos como "Indemnification against all claims", "Consideration" o "Injunctive Relief" NO funcionan tal cual en Colombia — hay que mapearlos a Responsabilidad Civil (contractual/extracontractual), Causa Lícita y Medidas Cautelares del Código Civil y de Comercio colombiano.

### "Contrato Ágil con Precio Fijo Estricto"
Pactar Scrum con precio cerrado y cronograma rígido tipo Waterfall sin Change Control Procedure. Genera disputas por sobrecostos. **Solución:** incluir siempre cláusula de cambios de alcance con procedimiento de cotización y aprobación.

## Frameworks internacionales recomendados

Para contratos B2B de Cifra, la combinación más efectiva es:
1. **Arquitectura de carátula (Common Paper)** — condiciones negociables en 2 páginas
2. **Lenguaje claro y diseño visual (WCC)** — tablas, flujos, sin legalese
3. **DPA adaptado a Ley 1581** — anexo de datos obligatorio
4. **Firma electrónica válida (Ley 527)** — DocuSign/AutenTIC con estampa cronológica

Ver `references/frameworks-estandares-internacionales.md` para detalles, glosario completo y tendencias 2024-2026.
