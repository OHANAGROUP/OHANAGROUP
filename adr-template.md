# ADR-[Número]: [Título de la Decisión Arquitectónica]

- **Fecha:** [YYYY-MM-DD]
- **Estado:** [Propuesto | Aceptado | Rechazado | Superado por [ADR-XXXX](url)]
- **Autor/es:** [Nombre/s]

---

## 📝 1. Context

*Describe el contexto técnico del sistema, las restricciones de hardware o infraestructura, las necesidades operativas y los cuellos de botella reales que obligan a tomar una decisión. Mantén esta sección puramente objetiva, analítica y basada en datos.*

> **Ejemplo:** "Nuestras tareas de procesamiento de texto con LLMs remotos (APIs comerciales) añaden una latencia promedio de 2.8s por petición y elevan los costes operativos a $X,000 USD mensuales bajo cargas pico. Además, el manejo de datos altamente regulados restringe el envío de contraseñas y registros financieros a servidores de terceros, lo que nos obliga a evaluar infraestructuras perimetrales (edge) y de inferencia local."

---

## 🛠️ 2. Decision

*Define de forma clara y explícita cuál es la solución técnica elegida y bajo qué supuestos de diseño se implementa.*

> **Ejemplo:** "Seleccionamos la integración de **Ollama** corriendo de forma local como motor de inferencia principal en hardware dedicado de bajo consumo (hilos controlados de CPU en el servidor perimetral HP), acoplado a un pool dinámico de reintentos asíncronos en memoria."

---

## ⚖️ 3. Tradeoffs

*Toda decisión de arquitectura seria tiene pros y contras explícitos. Documenta qué balances operacionales estás aceptando conscientemente al implementar esta opción.*

- **Pros (Ganancias Operativas):**
  - *Soberanía de Datos:* Garantía de cumplimiento al procesar el 100% de la información sensible offline.
  - *Coste de Inferencia:* Reducción de costes recurrentes a $0 USD/mes por inferencia local en CPU.
- **Cons (Costes de Diseño):**
  - *Rendimiento:* La latencia de procesamiento local en CPU es de ~25 tokens/s frente a los ~80 tokens/s de una GPU en nube.
  - *Mantenibilidad:* Incremento del overhead en el equipo al tener que administrar procesos del demonio Ollama e hilos del sistema operativo localmente.

---

## 📈 4. Consequences

*Describe los efectos que tiene esta decisión en el desarrollo del sistema a corto y largo plazo (cambios en el codebase, testing, despliegue, dependencias).*

- **En el Codebase:** Necesidad de implementar un handler de reintentos y lógica de timeout personalizado en `src/core/inference.py`.
- **En la Infraestructura:** El despliegue de Docker Compose requiere montar volúmenes específicos para almacenar los pesos de los modelos locales (`llama3.2`) sin redundancia.
- **En el Testing:** Obliga a crear fixtures de mocks para pruebas de integración offline y simulaciones de timeout.

---

## ❌ 5. Rejected Alternatives

*Detalla qué otras alternativas serias se evaluaron y por qué fueron descartadas técnicamente.*

### Alternativa A: [Nombre de la Alternativa Descartada (ej: Llama.cpp raw bindings)]
- **Razones del descarte:** "Aunque ofrece un overhead de memoria ligeramente inferior (~80MB menos), requiere compilar binarios específicos de C++ para cada sistema anfitrión, lo que rompe nuestro principio de **'small-team maintainability'** y portabilidad rápida a través de Docker."

### Alternativa B: [Nombre de la Alternativa Descartada (ej: API Cloud Dedicada)]
- **Razones del descarte:** "Descartada por incumplir nuestra restricción de seguridad central de no enviar llaves criptográficas ni balances fiscales a redes externas."
