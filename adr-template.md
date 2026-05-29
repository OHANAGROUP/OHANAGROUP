# [Número y Título Corto de la Decisión]

- **Fecha:** [YYYY-MM-DD]
- **Estado:** [Propuesto | Aceptado | Rechazado | Superado por [ADR-XXXX](url)]
- **Autor/es:** [Nombre/s]
- **Decisiones previas relacionadas:** [Enlace a ADRs anteriores, si aplica]

---

## Contexto técnico y Problemática

*Describe el contexto del sistema, las restricciones, las necesidades del negocio o los cuellos de botella que nos obligan a tomar una decisión. Mantén esto objetivo y basado en hechos operativos.*

Ejemplo:
> "Nuestra plataforma procesa tareas de inferencia de lenguaje que consumen alta latencia en APIs externas (Claude/OpenAI). Con el aumento de volumen, los costos de inferencia escalaron a más de $X,000 USD/mes, y los clientes demandan privacidad absoluta de sus datos empresariales (no compartir datos con proveedores cloud)."

---

## Opciones Evaluadas

Detalla las alternativas consideradas con sus pros y contras objetivos:

### Opción 1: [Nombre de la Alternativa 1]
- **Pros:**
  - [Beneficio 1]
  - [Beneficio 2]
- **Contras:**
  - [Limitación 1]
  - [Limitación 2]

### Opción 2: [Nombre de la Alternativa 2]
- **Pros:**
  - [Beneficio 1]
- **Contras:**
  - [Limitación 1]

---

## Decisión Seleccionada

*Explica claramente cuál fue la opción ganadora y por qué.*

> "Seleccionamos la **Opción X** porque..."

---

## Consecuencias y Compromisos (Trade-offs)

Toda decisión arquitectónica tiene consecuencias. Es importante documentarlas:

- **Impacto positivo (Beneficios):**
  - [¿Qué ganamos?]
- **Impacto negativo / Costos:**
  - [¿Qué perdemos o qué nuevas complejidades introducimos?]
- **Efectos a largo plazo:**
  - [¿Cómo afecta al mantenimiento, despliegue o escalabilidad del sistema?]
