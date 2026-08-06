---
name: proteccion-datos-operativa
description: >  Skill operativa para cumplimiento de protección de datos personales en Colombia (Ley 1581/2012). Redacta contratos de encargado, políticas de privacidad, avisos de privacidad, procedimientos ARCO, y guía el RNBD ante la SIC. Usa SIEMPRE que el usuario mencione: Ley 1581, protección de datos, datos personales, habeas data, SIC, RNBD, política de privacidad, aviso de privacidad, autorización de datos, contrato de encargado, transmisión de datos, transferencia internacional, incidente de seguridad, fuga de datos, derechos ARCO, supresión de datos, Decreto 1377, Ley 1266, habeas data financiero, datos sensibles, consentimiento informado, tratamiento de datos, o cuando necesite documentos operativos de cumplimiento de protección de datos en Colombia. Complementa a data-ethics-governance (frameworks internacionales) con el trabajo legal operativo colombiano.
---

# Protección de Datos Operativa — Colombia (Ley 1581/2012)

## ⚠️ Regla #0: No soy abogado, pero sí soy operativo

Esta skill produce documentos operativos de cumplimiento — contratos de encargado, políticas, procedimientos — que en la práctica constituyen el 90% del trabajo de protección de datos de una empresa tech pequeña. Para validación formal ante la SIC o en caso de incidente con riesgo de sanción, recomendar abogado especializado.

## Diferencia con data-ethics-governance

| Esta skill (proteccion-datos-operativa) | data-ethics-governance |
|----------------------------------------|----------------------|
| Ley 1581, Decreto 1377, Ley 1266 | GDPR, NIST AI RMF, ISO 42001 |
| Redacta contratos y políticas | Evalúa sesgos y fairness |
| Guía RNBD ante la SIC | Diseña frameworks de gobernanza |
| Operativa y documental | Teórica y estratégica |

## Cuándo leer los archivos de referencia

- **Redactar contrato de encargado o política** → `references/ley-1581-completa.md`
- **Reglamentación operativa (autorizaciones, procedimientos)** → `references/decreto-1377.md`
- **Datos financieros/crediticios** → `references/ley-1266-habeas-data.md`
- **Implementar programa integral (PIGDP)** → `references/guia-pigdp-sic.md`

## Mapa de decisión: ¿Qué documento necesita?

```
¿Qué necesita el usuario?
│
├── CONTRATO de encargado del tratamiento
│   ├── Cifra es ENCARGADO (procesa datos por cuenta del cliente)
│   ├── Elementos: finalidad, tipo de datos, medidas de seguridad, devolución/eliminación
│   └── Lee ley-1581-completa.md (Arts. 17-18) + decreto-1377.md (Arts. 3.1-3.2)
│
├── POLÍTICA de tratamiento de datos
│   ├── Documento público obligatorio (Art. 13, Decreto 1377)
│   ├── Contenido: identidad del responsable, derechos ARCO, finalidades, procedimiento
│   └── Lee decreto-1377.md + guia-pigdp-sic.md
│
├── AVISO de privacidad
│   ├── Versión corta para recolección en formularios web, correo, presencial
│   ├── Debe informar: responsable, finalidad, derechos, dónde consultar política completa
│   └── Lee decreto-1377.md (Art. 14)
│
├── AUTORIZACIÓN de tratamiento
│   ├── Formato de consentimiento previo, expreso e informado
│   ├── Medios válidos: escrito, oral, conducta inequívoca (Art. 7, Decreto 1377)
│   └── Datos sensibles requieren autorización EXPRESA (Art. 6, Ley 1581)
│
├── PROCEDIMIENTO de atención de derechos ARCO
│   ├── Consultas: 10 días hábiles (Art. 14, Ley 1581)
│   ├── Reclamos: 15 días hábiles (Art. 15, Ley 1581)
│   └── Lee ley-1581-completa.md (Arts. 14-15)
│
├── REGISTRO NACIONAL DE BASES DE DATOS (RNBD)
│   ├── Obligatorio para toda persona que trate datos personales
│   ├── Registro ante la SIC (plataforma virtual)
│   └── Lee guia-pigdp-sic.md
│
└── RESPUESTA A INCIDENTE de seguridad
    ├── Notificación en 48h al responsable (cláusula contractual típica)
    ├── No hay plazo legal explícito en Ley 1581, pero la SIC espera inmediatez
    └── Documentar: naturaleza, alcance, datos afectados, medidas de contención
```

## Clasificación de datos — Tabla operativa

| Tipo de dato | Definición | Tratamiento | Ejemplo |
|-------------|-----------|-------------|---------|
| **Público** | Contenido en documentos públicos, actos de autoridad | Libre tratamiento | NIT, razón social |
| **Semiprivado** | No íntimo pero de acceso limitado | Requiere autorización | Datos financieros/crediticios |
| **Privado** | Interesa solo al titular | Requiere autorización expresa | Teléfono, dirección, correo |
| **Sensible** | Afecta intimidad, uso indebido puede generar discriminación | Prohibido salvo excepciones del Art. 6 | Salud, orientación sexual, biometría |

## Principios rectores (Art. 4, Ley 1581)

1. **Legalidad** — tratamiento sujeto a ley vigente
2. **Finalidad** — tratamiento obedece a finalidad legítima informada al titular
3. **Libertad** — solo con consentimiento previo, expreso e informado
4. **Veracidad** — información veraz, completa, actualizada
5. **Transparencia** — derecho a conocer la existencia del tratamiento
6. **Acceso y circulación restringida** — solo personas autorizadas
7. **Seguridad** — medidas técnicas para evitar adulteración, pérdida, acceso no autorizado
8. **Confidencialidad** — reserva de la información

## Sanciones (Art. 23, Ley 1581)

- Multas hasta **2.000 SMMLV** (~$2.600 millones COP en 2026)
- Suspensión de actividades de tratamiento hasta por **6 meses**
- Cierre temporal de operaciones que involucren datos sensibles
- Cierre inmediato y definitivo si involucra datos de menores

## Transferencia internacional de datos

- Permitida a países con nivel adecuado de protección (declarado por SIC)
- A países sin nivel adecuado: requiere autorización previa de la SIC o del titular
- **Excepción cloud:** la SIC ha aceptado que el almacenamiento en nube (AWS, GCP, Azure) no constituye transferencia per se si hay contrato de encargado con cláusulas de seguridad
- **Implicación para Cifra:** cuando se usan APIs de IA (Claude, GPT) con datos personales, se está transmitiendo datos a servidores fuera de Colombia — necesita autorización o anonimización previa

## Formato de entregables

1. Siempre preguntar: ¿quién es responsable y quién encargado? ¿qué datos se tratan? ¿hay datos sensibles?
2. Generar documentos en lenguaje formal jurídico
3. Incluir todas las cláusulas obligatorias según la normativa
4. Ofrecer generar como .docx descargable
5. Si el contexto involucra IA, cruzar con las recomendaciones de data-ethics-governance
