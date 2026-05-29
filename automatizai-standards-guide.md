# Manual de Estandarización de Ingeniería — AutomatizAI

Este documento establece el **Marco de Estandarización y Creación de Nuevos Productos** para AutomatizAI. Implementar este marco garantiza que cada nuevo vertical o producto SaaS que lancemos al mercado comparta la misma robustez técnica, resiliencia y madurez operativa que nuestro repositorio insignia.

Nuestra filosofía de ingeniería se resume en una premisa central: **"Las restricciones también son arquitectura"**.

---

## ❓ 1. ¿Qué significa "Estandarizar" en AutomatizAI?

Estandarizar **no es agregar burocracia ni hacer más lento el desarrollo**. Para una startup ágil o un equipo de desarrollo *bootstrapped*, estandarizar significa **crear un sistema repetible de buenas decisiones de diseño y documentación**. 

Al estandarizar, logras tres objetivos críticos:
1.  **Velocidad de Lanzamiento:** No reinventamos la rueda en cada producto. La estructura de carpetas, la base de datos, la seguridad y la telemetría se inicializan de la misma manera.
2.  **Mantenibilidad por Equipos Pequeños:** Cualquier desarrollador (o tú mismo en el futuro) puede entrar a un repositorio nuevo y entender la arquitectura, las restricciones y las decisiones críticas en menos de 10 minutos.
3.  **Valor de Portafolio / Auditoría (Investor-Ready):** Si un cliente corporativo grande, un socio técnico o un inversionista audita el GitHub de AutomatizAI, no verá prototipos caóticos dispersos. Verá un ecosistema de ingeniería coherente de nivel Staff.

---

## 📂 2. Estructura de Proyecto Estándar (El "AutomatizAI Boilerplate")

Cada nuevo repositorio que creemos en AutomatizAI debe seguir estrictamente esta estructura de directorios inicial:

```txt
├── .github/workflows/    # Automatizaciones CI/CD (Linter, tests automáticos)
├── config/               # Configuraciones de entorno y telemetría por defecto
├── docs/
│   ├── adr/              # Repositorio de Architecture Decision Records (ADRs)
│   │   ├── 0000-template.md
│   │   └── 0001-core-architecture-selection.md
│   ├── images/           # Capturas de pantallas operativas de monitoreo
│   └── roadmap.md        # Plan de optimización y desarrollo
├── src/
│   ├── api/              # Gateway FastAPI (Routers, middleware y validación)
│   ├── core/             # Criptografía (AES, HMAC), sincronización y utilidades de DB
│   ├── pipeline/         # Orquestación de flujos de negocio y reglas
│   └── workers/          # Workers asíncronos de procesamiento de colas locales
├── tests/                # Pruebas unitarias, de integración y evals de LLMs
├── .gitignore
├── docker-compose.yml    # Entorno local contenerizado y aislado
├── README.md             # Documento de Criterio Operativo y Principios de Diseño
└── requirements.txt / package.json
```

---

## ⚡ 3. Principios y Límites de Diseño (Systems Engineering)

Cada nuevo producto debe incorporar activamente las siguientes secciones en su `README.md` antes de iniciar la fase de desarrollo.

### A. Failure Modes (Modos de Fallo Conocidos)
El software real falla. Documentar explícitamente cómo fallará tu sistema fomenta la humildad técnica y la preparación para la observabilidad:
-   **SQLite WAL lock contention:** Ocurre ante ráfagas masivas de escritura concurrente en la cola de tareas local.
-   **Ollama model thrashing:** Ocurre durante solicitudes de inferencia paralela pesada cuando la memoria RAM/VRAM se satura al intercambiar modelos de forma abrupta.
-   **Network starvation:** Ocurre durante ciclos de sincronización asíncrona de base de datos en entornos con internet inestable.
-   **Playwright session memory leaks:** Ocurre ante ejecuciones prolongadas de sesiones del navegador web sin recolección de basura adecuada.

### B. Operational Budget (Presupuesto de Operaciones y Recursos)
Diseñamos software óptimo y eficiente para conservar runway financiero y evitar despliegues sobredimensionados:
-   **CPU/RAM Limits:** Diseñado para operar en entornos ajustados de **<= 4 vCPUs** y **<= 16GB RAM**.
-   **Inference Priority:** Se prioriza la inferencia en modelos locales para tareas rutinarias y de baja prioridad.
-   **Cloud Inference:** Reservado estrictamente para operaciones de alta confianza o validación compleja.
-   **Infrastructure Sprawl:** Mantener la infraestructura mínima siempre encendida para reducir costes fijos a cerca de $0 USD.

### C. Complexity Budget (Presupuesto de Complejidad)
Toda tecnología introducida añade una deuda de mantenimiento a largo plazo. **Prohibimos explícitamente el uso de las siguientes tecnologías a menos que la evidencia operativa demuestre su absoluta necesidad:**
-   No Kubernetes / No orquestadores de contenedores complejos.
-   No bases de datos de consenso distribuido (e.g., Consul, Etcd).
-   No buses de eventos distribuidos complejos (e.g., Kafka, RabbitMQ pesado).
-   No microservicios innecesarios (preferir monolitismo modular perimetral).
-   No bases de datos vectoriales dedicadas externas pesadas (preferir SQLite-VSS o PGVector local).

### D. Observability First (Requerimientos de Observabilidad)
Ningún servicio se despliega a ciegas. Todo componente debe exponer de forma nativa:
-   **Health endpoint (`/health`):** Para monitorización automática de disponibilidad.
-   **Structured Logs:** Logs formateados en JSON serializado con niveles e ID de correlación de tareas.
-   **Latency metrics:** Medición de tiempo de ejecución de tareas e inferencia.
-   **Error counters:** Rastreo y conteo de excepciones por módulo.
-   **Queue depth metrics:** Profundidad y throughput de la cola de tareas local.

### E. Escalation Philosophy (Filosofía de Escalado de Errores)
Diseñamos flujos tolerantes a fallos con un orden de mitigación claro y estructurado:
1.  **Local recovery:** El worker local intenta mitigar el fallo mediante hotswap o limpieza interna.
2.  **Retry with backoff:** Reintento automático asíncrono con retraso exponencial para evitar saturar el sistema.
3.  **Graceful degradation:** Si el fallo persiste, el sistema degrada su funcionalidad de manera controlada (ej: retornar un resultado simplificado) antes de colapsar.
4.  **Human intervention:** Alerta estructurada al operador técnico solo cuando las fases anteriores han fallado.
*Queda prohibido implementar retintentos automáticos en cascada ciegos entre múltiples proveedores comerciales que puedan disparar los costes financieros.*

---

## 📄 4. ADRs (Architecture Decision Records) como Primera Clase

En AutomatizAI, **el razonamiento técnico vale más que el código**. Por ello, cualquier cambio crítico en la arquitectura o selección de tecnología requiere redactar un ADR de 5 bloques:

```txt
ADR-0007: Why PowerShell was selected for watchdog automation instead of Python daemons
```

### Plantilla Estándar (docs/adr/0000-template.md):
1.  **Context:** Contexto de sistemas, restricciones físicas y necesidades reales del negocio.
2.  **Decision:** Opción elegida y justificación técnica objetiva.
3.  **Tradeoffs:** Balances explícitos aceptados de forma consciente (Pros/Cons).
4.  **Consequences:** Impacto a corto y largo plazo en el codebase, despliegue y testing.
5.  **Rejected Alternatives:** Opciones descartadas y su por qué técnico detallado.

---

## 🔄 5. El Ciclo de Creación de un Producto Nuevo (Paso a Paso)

### Paso 1: Definir los Límites en el `README.md`
Inicializa el repositorio con la estructura de carpetas estándar y define los *Non-Goals*, *Known Constraints*, *Failure Modes* y *Budgets* iniciales.

### Paso 2: Documentar las Decisiones de Arquitectura (ADRs)
Crea los registros iniciales en `docs/adr/` justificando la selección del stack base.

### Paso 3: Desarrollo Incremental y Commits Semánticos
Escribe commits claros siguiendo **Conventional Commits** y realiza verificaciones de código constantes.

### Paso 4: Operational Review (Lista de Verificación de Producción)
Antes de desplegar cualquier cambio a producción, el producto debe aprobar esta lista de verificación:
-   [ ] **Failure Modes:** ¿Están los modos de fallo identificados y documentados en el README?
-   [ ] **Metrics Exposed:** ¿Están las métricas de rendimiento y la salud expuestas en endpoints correctos?
-   [ ] **Retry Policy:** ¿Se ha definido una política de reintento con backoff exponencial adecuada?
-   [ ] **Cost Impact:** ¿Se ha estimado el impacto financiero máximo en llamadas de inferencia comerciales?
-   [ ] **Recovery Path:** ¿Se ha probado de forma interactiva el comportamiento del sistema ante la caída de un worker?
-   [ ] **Logs Structured:** ¿Los logs se emiten en formato JSON estructurado con ID de tarea?
