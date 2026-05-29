# Manual de Estandarización de Ingeniería — AutomatizAI

Este documento establece el **Marco de Estandarización y Creación de Nuevos Productos** para AutomatizAI. Implementar este marco garantiza que cada nuevo vertical o producto SaaS que lancemos al mercado comparta la misma robustez técnica, resiliencia y madurez operativa que nuestro repositorio insignia.

---

## ❓ 1. ¿Qué significa "Estandarizar" en AutomatizAI?

Estandarizar **no es agregar burocracia ni hacer más lento el desarrollo**. Para una startup como AutomatizAI, estandarizar significa **crear un sistema repetible de buenas decisiones de diseño y documentación**. 

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

## 🔄 3. El Ciclo de Creación de un Producto Nuevo (Paso a Paso)

Para implementar esta estandarización en tus flujos de trabajo en AutomatizAI, sigue este proceso de 4 pasos al iniciar cualquier desarrollo:

### Paso 1: Definir los Límites en el `README.md` (Antes de programar)
Antes de escribir la primera línea de código, inicializa el repositorio con un `README.md` que responda a estas preguntas básicas. Esto evitará la proliferación de infraestructura innecesaria (*overengineering*):
-   **¿Qué es el producto y cuáles son sus Principios de Diseño?** (ej: *Offline-first*, *Graceful degradation*).
-   **¿Cuáles son sus Non-Goals?** (¿Qué es lo que explícitamente **no** va a resolver este software para mantener el foco?).
-   **¿Cuáles son sus Known Constraints?** (Límites físicos, limitaciones de llamadas o contención de base de datos conocidas).
-   **¿Cuáles son sus Operational Constraints?** (ej: diseñado para mantenimiento con 1 desarrollador, inferencia local de bajo costo).

### Paso 2: Documentar las Decisiones de Arquitectura (ADRs)
Toda decisión tecnológica crítica (ej: usar SQLite en lugar de PostgreSQL para almacenamiento local, elegir Playwright en lugar de Selenium, usar un modelo de Ollama específico) debe tener su propio archivo en `docs/adr/` utilizando la plantilla de 5 bloques:
1.  **Context:** ¿Qué problema técnico y restricciones físicas nos obligan a decidir?
2.  **Decision:** ¿Cuál es la opción elegida y bajo qué parámetros?
3.  **Tradeoffs:** ¿Qué ganamos y qué perdemos conscientemente con esta elección?
4.  **Consequences:** ¿Cómo impacta al código y al testing a largo plazo?
5.  **Rejected Alternatives:** ¿Qué otras opciones evaluamos y por qué las descartamos?

### Paso 3: Desarrollo Incremental y Commits Semánticos
El código de producción de AutomatizAI debe mantenerse limpio y trazable.
-   Utiliza **Conventional Commits** (`feat:`, `fix:`, `docs:`, `refactor:`) para cada commit.
-   Prohíbe los commits vacíos o genéricos.
-   Cada Pull Request debe incluir una sección de *Tradeoffs* y cómo se verificaron los cambios de manera operativa.

### Paso 4: Monitoreo y Telemetría desde el Día 1
Ningún producto de AutomatizAI se considera finalizado si no tiene telemetría básica visible.
-   Integra logs estructurados en formato JSON.
-   Expón métricas básicas para Prometheus (latencia, porcentaje de acierto del caché, tamaño de cola de tareas).
-   Toma screenshots operacionales reales y guárdalos bajo `/docs/images/` para documentar el estado en producción.

---

## 🚀 4. Ejemplo Práctico: Creando el Producto "NexFit"

Si tuviéramos que iniciar la creación de un nuevo SaaS (por ejemplo, **NexFit**, una calculadora/orquestador local para entrenadores o gimnasios), el proceso estandarizado se vería así:

1.  **Creación del Repo:** Inicializamos con el boilerplate estándar de carpetas.
2.  **El README de NexFit:** 
    -   *Positioning:* "Production-oriented offline scheduling and metric tracking engine for localized fitness centers."
    -   *Non-Goals:* "No proveeremos planes de entrenamiento generales por IA; no integraremos procesamiento en nube para datos biométricos sensibles de usuarios."
    -   *Operational Constraints:* "Diseñado para correr en una Raspberry Pi / hardware local del gimnasio con base de datos local SQLite y sincronización asíncrona."
3.  **El Primer ADR (`docs/adr/0001-local-storage.md`):** Documenta por qué se eligió SQLite local sobre una base de datos en nube analizando la tolerancia a caídas de internet en locales físicos (*intermittent connectivity*).
4.  **Desarrollo:** Commits limpios detallando la construcción de los workers locales.
