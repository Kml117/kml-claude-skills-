---
name: laboral-contratistas-col
description: >  Gestiona relación laboral y contratistas en startups colombianas. Diferencia contrato laboral vs. prestación de servicios, calcula prestaciones, guía seguridad social y previene relación laboral encubierta. Usa SIEMPRE que el usuario mencione: contrato laboral, contrato de trabajo, prestación de servicios, contratista, freelancer, prestaciones sociales, prima, cesantías, vacaciones, liquidación laboral, nómina, seguridad social, EPS, AFP, ARL, pensión, parafiscales, SENA, ICBF, subordinación, outsourcing, cuenta de cobro, planilla PILA, aportes, salario integral, salario mínimo, auxilio de transporte, periodo de prueba, despido, indemnización, o cuando necesite contratar, vincular o desvincular personas en su empresa. También cuando Cifra necesite subcontratar un data scientist o desarrollador.
---

# Laboral y Contratistas — Colombia para Startups

## ⚠️ Regla #0: No soy abogado laboral

Esta skill orienta sobre modalidades de contratación, prestaciones y seguridad social, pero conflictos laborales y liquidaciones complejas deben consultarse con abogado laboral o contador.

## Cuándo leer los archivos de referencia

- **Contrato de trabajo, prestaciones, terminación** → `references/codigo-sustantivo-trabajo.md`
- **Riesgos laborales para contratistas** → `references/ley-1562-riesgos.md`
- **Decreto reglamentario del sector trabajo** → `references/decreto-1072-trabajo.md`

## La pregunta clave: ¿Contrato laboral o prestación de servicios?

### Test de los 3 elementos (Art. 23, CST)

Existe contrato de trabajo cuando se reúnen **simultáneamente**:
1. **Actividad personal** — el trabajador presta el servicio personalmente
2. **Subordinación** — el empleador da órdenes sobre modo, tiempo y lugar
3. **Remuneración** — hay un salario como contraprestación

Si los 3 elementos están presentes, **es contrato de trabajo independientemente del nombre que le pongan las partes** (principio de primacía de la realidad, Art. 53, Constitución).

### Cuándo usar cada modalidad

| Usar **prestación de servicios** cuando... | Usar **contrato laboral** cuando... |
|-------------------------------------------|-------------------------------------|
| El contratista define su horario | Hay horario fijo |
| Usa sus propios medios/herramientas | Usa herramientas del empleador |
| Asume riesgo comercial | El riesgo es del empleador |
| Trabaja para varios clientes | Dedicación exclusiva |
| El resultado importa, no el proceso | El proceso está controlado por el empleador |
| **Ejemplo:** Cifra contrata un data scientist freelancer para un proyecto específico de 2 meses | **Ejemplo:** Cifra contrata un developer de tiempo completo que reporta diariamente |

### Riesgo de "contrato realidad"

Si un juez determina que existía relación laboral encubierta bajo un contrato de prestación de servicios:
- **Pago retroactivo** de todas las prestaciones (prima, cesantías, vacaciones, intereses)
- **Indemnización** por terminación sin justa causa
- **Aportes a seguridad social** retroactivos (salud, pensión, ARL)
- **Sanciones** por no pago oportuno de prestaciones (Art. 65, CST — indemnización moratoria)

## Costos de un empleado formal en Colombia (2026)

| Concepto | % sobre salario | Quién paga |
|----------|----------------|-----------|
| **Salud** | 12.5% (8.5% empleador + 4% trabajador) | Compartido |
| **Pensión** | 16% (12% empleador + 4% trabajador) | Compartido |
| **ARL** | 0.522% — 6.96% según riesgo | Empleador |
| **Caja de compensación** | 4% | Empleador |
| **SENA** | 2% (exento si empresa es beneficiaria Ley 1607) | Empleador |
| **ICBF** | 3% (exento si empresa es beneficiaria Ley 1607) | Empleador |
| **Prima de servicios** | ~8.33% (1 mes/año) | Empleador |
| **Cesantías** | ~8.33% (1 mes/año) | Empleador |
| **Intereses sobre cesantías** | 1% mensual sobre cesantías | Empleador |
| **Vacaciones** | ~4.17% (15 días/año) | Empleador |
| **Dotación** | Variable (3 veces/año si salario ≤ 2 SMMLV) | Empleador |
| **TOTAL carga prestacional** | **~50-55% adicional** sobre el salario | — |

### Ejemplo: empleado con salario de $3.000.000 COP/mes
- Costo total para la empresa: ~$4.500.000 — $4.650.000 COP/mes
- El trabajador recibe en neto: ~$2.760.000 COP/mes (después de deducciones salud+pensión)

## Contratista independiente (prestación de servicios)

### Obligaciones de seguridad social del contratista
Desde la Ley 1562/2012, los contratistas deben estar afiliados a:
- **Salud** — el contratista paga el 12.5% sobre el 40% del valor del contrato
- **Pensión** — el contratista paga el 16% sobre el 40% del valor del contrato
- **ARL** — el **contratante** (Cifra) afilia y paga la ARL del contratista

### Base de cotización
- **IBC** (Ingreso Base de Cotización) = 40% del valor mensual del contrato
- Esta es la base sobre la que se calculan los aportes a salud y pensión

### Verificación obligatoria
El contratante (Cifra) **debe verificar** la afiliación y pago de seguridad social del contratista antes de cada pago. El contratista debe presentar su planilla PILA o certificado de pago.

### Modelo de contrato de prestación de servicios — Elementos mínimos

1. Identificación de contratante y contratista
2. Objeto del contrato (servicio específico)
3. Precio / honorarios
4. Plazo de ejecución
5. Independencia del contratista (cláusula explícita)
6. Obligación del contratista de pagar seguridad social
7. Obligación del contratante de afiliar a ARL
8. Confidencialidad y propiedad intelectual
9. Cláusula de no exclusividad (refuerza la independencia)

## Contratación de extranjeros

Si Cifra contrata un freelancer en el exterior (remoto):
- No aplica el CST colombiano (la relación se rige por la ley del domicilio del contratista)
- Sin obligación de seguridad social colombiana
- Cifra genera **documento soporte** para deducción fiscal (tributario-startup-col)
- Considerar implicaciones de protección de datos si se transmiten datos personales

## Formato de entregables

1. Para contratos: preguntar modalidad (laboral vs. servicios), valor, duración
2. Para liquidaciones: solicitar salario, fechas de ingreso/retiro, conceptos pendientes
3. Para aportes: calcular IBC y porcentajes según régimen
4. Siempre alertar sobre el riesgo de contrato realidad cuando la modalidad sea ambigua
5. Ofrecer generar contrato como .docx descargable
