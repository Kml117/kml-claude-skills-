---
name: propiedad-intelectual-software
description: >  Gestiona la propiedad intelectual de software, bases de datos y productos digitales en Colombia y la Comunidad Andina. Cubre registro ante la DNDA, cláusulas de cesión/licencia de IP, secretos empresariales y licencias open source. Usa SIEMPRE que el usuario mencione: propiedad intelectual, derechos de autor, copyright, DNDA, registro de software, cesión de derechos, licencia de software, open source, GPL, MIT, Apache, secreto empresarial, obra por encargo, Decisión Andina 351, marca, PI, IP, propiedad del código, quién es dueño del código, licenciamiento, Ley 23 de 1982, Ley 1915, o cuando pregunte sobre titularidad, protección o explotación de software, datos, modelos de IA o activos digitales. Clave para el escenario Cifra: quién es dueño del código desarrollado para un cliente.
---

# Propiedad Intelectual de Software — Colombia y Comunidad Andina

## ⚠️ Regla #0: No soy abogado

Produce análisis, borradores de cláusulas y guía de registro. Para litigios de PI o registro de marcas/patentes, recomendar abogado especializado.

## Cuándo leer los archivos de referencia

- **Marco supranacional (prevalece sobre ley nacional)** → `references/decision-andina-351.md`
- **Ley nacional de derechos de autor** → `references/ley-23-derechos-autor.md`
- **Reforma digital 2018** → `references/ley-1915-reforma.md`
- **Cómo registrar software** → `references/guia-registro-dnda.md`

## Regla de oro: ¿Quién es dueño del software?

Esta es la pregunta más frecuente y la respuesta depende del contexto:

| Contexto | Titularidad | Fundamento legal |
|----------|------------|-----------------|
| **Obra creada bajo relación laboral** | El empleador | Art. 20, Decisión 351; Art. 20, Ley 23/1982 |
| **Obra por encargo (contrato de servicios)** | El contratista (quien crea), SALVO pacto en contrario | Art. 20, Decisión 351 |
| **Obra con cesión contractual** | Quien la compra, según el contrato | Autonomía de voluntad + Art. 182, Ley 23/1982 |
| **Software de Cifra para un cliente** | **Depende del contrato** — si no dice nada, Cifra (creador) retiene los derechos | Decisión 351, Art. 20 |

### Implicación crítica para Cifra

> Si el contrato de desarrollo con Security Global (o cualquier cliente) NO incluye una cláusula de cesión de propiedad intelectual, **Cifra retiene los derechos de autor sobre el código**. Esto puede ser un problema para el cliente y una ventaja para Cifra dependiendo del modelo de negocio. **Siempre incluir cláusula de IP explícita.**

### Patrón recomendado para Cifra

```
CESIÓN: Al completar el pago total, Cifra cede al cliente los derechos patrimoniales
sobre el código específico del proyecto (código fuente y objeto).

RETENCIÓN: Cifra retiene:
(a) Herramientas, frameworks, librerías y componentes preexistentes
    (otorgando licencia perpetua de uso al cliente)
(b) Conocimiento general, metodologías y know-how
(c) Derecho de referencia comercial (caso de estudio sin datos sensibles)

DERECHOS MORALES: Los derechos morales (paternidad, integridad) son
inalienables e imprescriptibles y permanecen con los autores (Art. 11, Decisión 351).
```

## Software como obra literaria

En la Comunidad Andina, el software se protege como **obra literaria** (Art. 23, Decisión 351). Esto significa:

- **NO necesita registro** para existir la protección (nace con la creación)
- Pero el **registro ante la DNDA** genera presunción de autoría y fecha cierta
- La protección dura la **vida del autor + 70 años** (persona natural) o **70 años desde publicación** (persona jurídica)
- Las **ideas, algoritmos y conceptos** NO se protegen; solo la expresión (código)

## Registro ante la DNDA — Proceso

1. Ingresar al portal de la DNDA (derechodeautor.gov.co)
2. Diligenciar formulario de registro de soporte lógico (software)
3. Adjuntar: descripción funcional + extractos del código fuente (no todo el código)
4. Pago de tasa (variable según tipo — consultar guia-registro-dnda.md)
5. Tiempo estimado: 15-30 días hábiles
6. Resultado: certificado de registro con presunción de autoría

## Licencias Open Source — Tabla rápida

| Licencia | Permisiva | Copyleft | Obligación principal | Compatible con cerrado |
|----------|-----------|----------|---------------------|----------------------|
| **MIT** | ✓ | ✗ | Incluir aviso de copyright | ✓ |
| **Apache 2.0** | ✓ | ✗ | Incluir aviso + NOTICE + patentes | ✓ |
| **BSD 2/3** | ✓ | ✗ | Incluir aviso de copyright | ✓ |
| **GPL v3** | ✗ | ✓ Fuerte | Código derivado debe ser GPL | ✗ |
| **LGPL v3** | ✗ | ✓ Débil | Solo la librería; linking permite cerrado | ✓ (con linking) |
| **AGPL v3** | ✗ | ✓ Red | Uso en servidor = distribución | ✗ |

### Regla para Cifra
- **Usar librerías MIT/Apache/BSD** en proyectos para clientes (sin riesgo)
- **Cuidado con GPL/AGPL** — si Cifra usa código GPL en un proyecto que cede al cliente, el cliente hereda la obligación copyleft
- **Documentar siempre** las dependencias open source en un archivo NOTICES o THIRD_PARTY_LICENSES

## Secretos empresariales

Protección alternativa/complementaria a derechos de autor para:
- Algoritmos propietarios
- Bases de datos curadas
- Modelos de ML entrenados (los pesos, no la arquitectura)
- Procesos de negocio

Requisitos (Art. 260+, Decisión 486 de la CAN):
1. La información debe ser secreta (no generalmente conocida)
2. Debe tener valor comercial por ser secreta
3. Se deben tomar medidas razonables para mantenerla secreta (NDAs, acceso restringido, cifrado)

## Formato de entregables

1. Para cláusulas de IP: usar el patrón recomendado y adaptarlo al caso
2. Para registro DNDA: guiar paso a paso con la guía de referencia
3. Para auditoría de licencias: listar dependencias y verificar compatibilidad
4. Marcar siempre la distinción entre derechos patrimoniales (cedibles) y morales (inalienables)
