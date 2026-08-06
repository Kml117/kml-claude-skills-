# Frameworks, Estándares Internacionales y Anti-Patrones para Contratación Tech en Colombia

## Fuente
Investigación propia (Camilo Córdoba Torres, julio 2026): "Arquitectura Legal y Operativa de la Contratación de Servicios Tecnológicos en Colombia: Marco Normativo, Estándares Internacionales y Automatización (2020–2026)"

---

## 1. FRAMEWORKS PRINCIPALES

### WCC Contract Design Framework (World Commerce & Contracting)
- **Qué es:** Estándar global de diseño contractual usado por +80,000 profesionales en +170 países.
- **Principios:** Lenguaje claro (Plain Language), diseño centrado en el usuario (tablas, diagramas de flujo), equilibrio comercial (Fairness), enfoque orientado al valor.
- **Arquitectura documental:** Modular → Contrato Marco (MSA) + Declaración de Trabajo (SoW) + Anexos Técnicos.
- **Cuándo usarlo:** Negociaciones B2B complejas, MSAs, desarrollo empresarial, licitaciones internacionales.
- **Cuándo NO:** Contratos masivos B2C de adhesión pura.

### oneNDA v2 (Claustack, 2022)
- **Qué es:** NDA estandarizado, texto inmodificable + carátula de variables. Licencia CC BY 4.0 (gratuito).
- **Adoptado por:** +3,000 organizaciones (PwC, UBS, Revolut, DataRobot).
- **Principio clave:** Solo se negocian las variables del encabezado; el articulado es fijo.
- **Cuándo usarlo:** Etapas exploratorias, evaluación de proveedores, due diligence, alianzas iniciales.
- **Cuándo NO:** Transacciones que exijan cesiones de IP específicas en la misma etapa.

### Common Paper (2022)
- **Qué es:** Estándares abiertos para contratos de software: Cloud Service Agreement (CSA), Professional Services Agreement (PSA), SLA, DPA.
- **Arquitectura de Carátula (Cover Page System):** Condiciones negociables en carátula frontal; condiciones generales fijadas por referencia en URL.
- **Cuándo usarlo:** Ventas SaaS B2B, servicios profesionales de implementación, SLAs comerciales.
- **Cuándo NO:** Contratación estatal colombiana (obligatorio SECOP/pliegos CCE).

### ANCP-CCE / MinTIC (Colombia Compra Eficiente)
- **Qué es:** Guías y pliegos tipo obligatorios para contratación pública de tecnología en Colombia.
- **Instrumento vigente:** IAD/SDA Software por Catálogo II (CCE-SNG-IAD-002-2024, vigente 2024-2027), >$1.7 billones COP.
- **Guía de PI 2024:** Reglas sobre titularidad de código fuente, cesión de derechos patrimoniales, licencias open source.
- **Cuándo usarlo:** Obligatorio en sector público; recomendado como buenas prácticas en privado de gran escala.

## 2. COMBINACIONES GANADORAS

### Combinación A: Suscripción Eficiente B2B
**Common Paper (carátula) + WCC (lenguaje claro) + DPA Ley 1581**
- Carátula de 2 páginas con condiciones comerciales
- Términos generales por referencia en URL no modificable
- Anexo DPA con autorizaciones de tratamiento de datos
- **Ideal para:** modelo de suscripción de Cifra (DSaaS mensual)

### Combinación B: Automatización de Confidencialidad
**oneNDA v2 + AutoNDA/SimpleDocs + Firma Electrónica Ley 527**
- Borrador estandarizado + flujo automático + estampa cronológica
- Equipos comerciales autogestionan envío y firma sin revisión jurídica individual
- Plena validez probatoria en Colombia

## 3. ANTI-PATRONES CONTRACTUALES

### "Copy-Paste Transfronterizo"
Importar contratos de Common Law sin adaptación local: conceptos como "Indemnification against all claims", "Consideration" o "Injunctive Relief" sin adecuarlos al régimen colombiano de Responsabilidad Civil, Causa Lícita o Medidas Cautelares. **Resultado:** cláusulas inaplicables o invalidadas por jueces colombianos.

### "Contrato Ágil con Precio Fijo Estricto"
Pactar ejecución bajo Scrum con precio global cerrado y cronograma rígido tipo Waterfall, sin Change Control Procedure. **Resultado:** disputas por sobrecostos e incumplimientos mutuos.

## 4. MAPA DE RELACIONES ENTRE FRAMEWORKS

| Framework | Relación | Con | Justificación |
|-----------|----------|-----|---------------|
| Common Paper | Compite con | Minutas B2B tradicionales | Estándar abierto vs. minutas defensivas |
| oneNDA | Compite con | NDAs propietarios | Texto universal vs. minutas individuales |
| WCC | Se complementa con | Todos los demás | Metodología de diseño aplicable a cualquiera |
| Ley 527 + Ley 1581 | Prerrequisito de | Todos en Colombia | Normativa imperativa, no opcional |

## 5. TIPOS DE CONTRATO Y ESTRUCTURA MODULAR

### Mapa del dominio (jerarquía)
1. **Desarrollo e Ingeniería de Software** — metodologías ágiles vs. tradicionales, cesión de IP, criterios de aceptación
2. **Confidencialidad (NDA/PIA)** — unilaterales/mutuos, secretos empresariales (Decisión 486 CAN), DPAs
3. **SLA y Soporte** — uptime %, RTO, RPO, categorización P1-P4, créditos por servicio
4. **T&C de Plataformas (SaaS/PaaS/IaaS)** — clickwrap, AUP, caps on liability, exit clauses
5. **Consultoría e Integración** — SoW vinculado a MSA, precio fijo vs. T&M vs. hitos, change control

### Esquemas de remuneración
- **Precio Fijo (Fixed Price):** riesgo para el proveedor, incentivo a eficiencia
- **Horas y Materiales (Time & Materials):** riesgo para el cliente, flexibilidad máxima
- **Hitos (Milestones):** riesgo compartido, el más común en desarrollo ágil

### Contratos de adhesión digitales
- **Clickwrap:** clic en "Acepto" — válido bajo Ley 527
- **Browsewrap:** uso implica aceptación — validez más débil
- **Sign-in wrap:** aceptación al crear cuenta — intermedio

## 6. GLOSARIO OPERATIVO

### Términos clave para redacción
- **Equivalencia Funcional:** documentos electrónicos = documentos en papel (si cumplen integridad y accesibilidad)
- **Obra por Encargo:** presunción legal: derechos patrimoniales del software → cedidos al contratante
- **Licencia Copyleft (GPL):** obras derivadas deben distribuirse bajo mismas condiciones
- **Licencia Permisiva (MIT, Apache):** permite reutilizar en software propietario
- **Cláusula de Indemnidad:** una parte mantiene libre de daño a la otra frente a reclamaciones de terceros
- **Cap on Liability:** techo financiero máximo por daños derivados de incumplimiento
- **Service Credits:** descuentos por incumplimiento de SLA
- **MTTR:** tiempo medio de reparación (Mean Time To Repair)
- **Uptime:** % de disponibilidad operativa en un período
- **Smart Contract:** programa auto-ejecutable en blockchain que ejecuta cláusulas contractuales
- **CLM:** Contract Lifecycle Management — plataformas de gestión automatizada del ciclo contractual
- **Playbook Contractual:** documento normativo con cláusulas preferidas, alternativas y redlines

## 7. TENDENCIAS 2024-2026

### IP sobre código generado por IA
Debate activo en Colombia y la CAN (Decisión 351) sobre si código generado por GitHub Copilot, Claude o GPT-4o puede ser objeto de protección por derechos de autor. **Implicación para contratos:** incluir cláusula que defina el tratamiento de código AI-generated.

### Topes de responsabilidad en cloud
Conflicto entre proveedores de nube (AWS/Azure/GCP — caps = último año de facturación) y entidades financieras colombianas que exigen indemnidad ilimitada por fuga de datos.

### Playbooks dinámicos con IA generativa
Cláusulas de SLA y precios que se ajustan automáticamente según perfil de riesgo del cliente, procesado por agentes inteligentes.

### Anexos éticos de IA
Incorporación de transparencia algorítmica, auditorías de sesgo e indemnidad contra infracciones de copyright por entrenamiento de modelos.

## 8. RECURSOS DE REFERENCIA

### Libros fundamentales (Colombia)
- **Derecho del Comercio Electrónico y de Internet** — Erick Rincón Cárdenas (4ª ed., 2020). Referencia definitiva.
- **Derecho, innovación y tecnología** — Juan Carlos Henao et al. (3ª ed., 2020). Universidad Externado.
- **Aspectos legales del emprendimiento en Colombia** — Erick Rincón Cárdenas (1ª ed., 2025). Startups tech.

### Datasets para IA contractual
- **CUAD** (NeurIPS 2021): 510 contratos, 13,000+ anotaciones, 41 categorías de riesgo. CC BY 4.0.
- **SUIN-JURISCOL + SECOP II**: corpus normativo colombiano + minutas reales de contratación pública.

### Thought leaders
- **Erick Rincón Cárdenas:** Director TicTank U. Rosario, referente en contratación electrónica Colombia.
- **Electra Japonas:** Co-fundadora oneNDA, CEO TLB.
- **Stefania Passera:** Pionera en Legal Design.
