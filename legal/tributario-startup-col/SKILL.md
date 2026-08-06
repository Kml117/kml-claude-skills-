---
name: tributario-startup-col
description: >  Guía operativa de obligaciones tributarias para startups y SAS colombianas: RUT, régimen Ordinario vs. SIMPLE, IVA, retención en la fuente, facturación electrónica DIAN y documento soporte. Usa SIEMPRE que el usuario mencione: RUT, DIAN, impuestos, IVA, retención en la fuente, rete-fuente, declaración de renta, factura electrónica, facturación electrónica, documento soporte, régimen simple, SIMPLE, régimen ordinario, responsable de IVA, UVT, ICA, obligaciones tributarias, planeación tributaria, calendario tributario, exógena, facturar, cómo facturar, cuánto pago de impuestos, o cuando pregunte sobre impuestos o cumplimiento fiscal de su empresa en Colombia. También cuando esté constituyendo una SAS y necesite entender las obligaciones fiscales post-formalización.
---

# Tributario para Startups — Colombia

## ⚠️ Regla #0: No soy contador ni asesor fiscal

Esta skill orienta sobre obligaciones tributarias y opciones de régimen, pero las declaraciones y liquidaciones deben ser preparadas o validadas por un contador público. La normativa tributaria colombiana cambia con frecuencia — verificar con búsqueda web antes de dar fechas o tarifas como definitivas.

## Cuándo leer los archivos de referencia

- **Impuestos de renta, IVA, retención** → `references/estatuto-tributario-startup.md`
- **Cómo facturar electrónicamente** → `references/facturacion-electronica-dian.md`
- **Fechas de declaración y pago** → `references/calendario-tributario.md`

## Primer paso después de constituir la SAS: el RUT

El RUT (Registro Único Tributario) es la identidad fiscal de la empresa ante la DIAN.

### Proceso
1. **PRE-RUT** — se tramita en el portal DIAN como parte de la constitución
2. **Registro en Cámara de Comercio** — con el PRE-RUT adjunto
3. **RUT definitivo** — la DIAN asigna el NIT automáticamente tras el registro mercantil
4. **Actualización del RUT** — agregar responsabilidades (IVA, retención, etc.)

### Responsabilidades tributarias típicas de una SAS tech

| Código | Responsabilidad | ¿Aplica a Cifra? |
|--------|----------------|-----------------|
| 05 | Impuesto de renta | Sí (siempre) |
| 07 | Retención en la fuente a título de renta | Sí (si tiene empleados o contratistas) |
| 09 | Retención en la fuente a título de IVA | Depende del régimen |
| 11 | Ventas régimen común (IVA) | Sí (servicios de software gravan IVA 19%) |
| 14 | Informante de exógena | Si supera topes de ingresos |
| 47 | Régimen Simple de Tributación | Opcional (alternativa al ordinario) |

## Régimen Ordinario vs. SIMPLE — Cuándo conviene cada uno

| Factor | Ordinario | SIMPLE |
|--------|-----------|--------|
| **Tarifa de renta** | 35% sobre renta líquida | 1.8% - 11.6% sobre ingresos brutos (según tabla) |
| **IVA** | Declaración bimestral | Reemplazado por impuesto unificado (pero se sigue cobrando al cliente) |
| **Retención** | Se practica y se le practican | No le practican retención |
| **ICA** | Declaración separada al municipio | Incluido en el SIMPLE |
| **Contabilidad** | Completa (NIF/NIIF para Pymes) | Simplificada |
| **Tope de ingresos** | Sin tope | Hasta 100.000 UVT anuales (~$4.900M COP en 2026) |
| **¿Conviene para Cifra?** | Si los gastos deducibles son altos | Si la facturación es baja-media y los gastos son bajos |

### Regla práctica para startups
- **Facturación < $200M COP/año con pocos gastos deducibles** → SIMPLE suele ser mejor
- **Facturación alta o muchos gastos deducibles (nómina, infraestructura)** → Ordinario puede dar menor carga efectiva
- **Siempre simular ambos escenarios** con el contador antes de elegir

## Facturación electrónica — Lo esencial

### ¿Quién está obligado?
Toda persona jurídica (SAS) y toda persona natural con ingresos superiores a 3.500 UVT están obligados a facturar electrónicamente.

### Proceso de habilitación
1. Actualizar el RUT con la responsabilidad de facturación
2. Obtener resolución de numeración de facturación en el portal DIAN
3. Elegir un proveedor tecnológico autorizado o software de facturación
4. Emitir facturas con todos los requisitos (Resolución DIAN 000165/2023)

### Requisitos de la factura electrónica
- Denominación "Factura de Venta"
- NIT y nombre del emisor y adquirente
- Numeración consecutiva autorizada
- Fecha de expedición
- Descripción de bienes/servicios
- Valor unitario, subtotal, IVA (19%), total
- Forma de pago
- Firma digital o electrónica del emisor
- Código QR y CUFE (Código Único de Factura Electrónica)

### Documento soporte
Cuando Cifra contrata a un freelancer (persona natural no obligada a facturar), Cifra debe generar un **documento soporte en adquisiciones** y reportarlo a la DIAN.

## IVA en servicios de tecnología

| Servicio | IVA |
|----------|-----|
| Desarrollo de software a medida | 19% |
| Licenciamiento de software | 19% |
| Consultoría de datos/IA | 19% |
| Software como servicio (SaaS) | 19% |
| Exportación de servicios (cliente fuera de Colombia) | Exento (0%) — Art. 481, lit. c, E.T. |

### Exportación de servicios — Oportunidad para Cifra
Si Cifra presta servicios a clientes fuera de Colombia, el IVA es **exento** (no excluido). Esto significa que Cifra puede solicitar devolución del IVA pagado en sus compras. Requisito: el servicio debe ser utilizado exclusivamente en el exterior.

## Calendario tributario — Recordatorio

Las fechas de declaración y pago dependen del último dígito del NIT de la empresa. Consultar `references/calendario-tributario.md` para las fechas específicas de 2026.

### Declaraciones principales de una SAS
- **Renta**: anual (abril)
- **IVA**: bimestral o cuatrimestral según ingresos
- **Retención en la fuente**: mensual
- **ICA**: según municipio (Bogotá: bimestral)
- **Exógena**: anual (entre abril y mayo)

## Formato de entregables

1. Para análisis de régimen: hacer simulación comparativa SIMPLE vs. Ordinario
2. Para guía de facturación: paso a paso con screenshots si se tiene acceso al portal
3. Siempre recomendar validación con contador
4. Mencionar fechas del calendario tributario cuando sean relevantes
