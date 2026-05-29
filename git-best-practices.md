# Guía de Buenas Prácticas de Git & Convenios

Este documento resume las convenciones de Git y GitHub que transmiten inmediatamente una madurez de nivel Senior/Principal a revisores de código y reclutadores de empresas como Vercel, OpenAI o Anthropic.

---

## 📌 1. Conventional Commits (Mensajes de Commit)

Evita a toda costa los commits genéricos (`fix`, `cambios`, `prueba`). Utiliza la especificación de **Conventional Commits**:

### Estructura:
```txt
<tipo>(<alcance>): <descripción corta en imperativo y minúsculas>

[cuerpo largo opcional]

[pie de página opcional para vincular a Issues (ej: Closes #12)]
```

### Tipos recomendados:
- **`feat`:** Una nueva funcionalidad para el usuario (ej: `feat(worker): add orphan task recovery loop`).
- **`fix`:** Corrección de un bug o error (ej: `fix(sqlite): serialize writes with mutex lock`).
- **`docs`:** Cambios exclusivos de documentación (ej: `docs(adr): add database fallback strategy`).
- **`refactor`:** Modificaciones de código que no corrigen bugs ni agregan features (ej: `refactor(ai): introduce provider health cache`).
- **`chore`:** Tareas repetitivas o de configuración que no modifican código fuente (ej: `chore(docker): isolate network edge routes`).
- **`test`:** Añadir o corregir pruebas unitarias o de integración.

---

## 📋 2. Estructura de un Issue Profesional

Un issue bien estructurado demuestra que piensas en términos de **arquitectura, reproducibilidad y claridad**.

### Ejemplo de Issue:
```markdown
# [Bug] Thread lock contention during heavy Ollama concurrent calls

### Operational Context
Under heavy parallel task runs on CPU/RAM, the local `Ollama` engine experiences locking exceptions, leading to an immediate queue backup in `Obrero Soberano`.

### Steps to Reproduce
1. Dispatch 10 parallel lead-scoring tasks via Next.js dashboard.
2. Watch Docker logs for container `ollama-container`.
3. Exception thrown: `ThreadContentionException: Lock timeout expired`.

### Proposed Technical Solution
- Implement a thread-constraint queue worker with standard mutex locks inside `src/core/database.py`.
- Introduce a keep-alive timeout control mechanism to hotswap model states gracefully.
```

---

## 🔀 3. Estructura de un Pull Request de Alta Señal

Cuando crees un PR, recuerda que el revisor podría no entender toda la complejidad del código. Tu descripción del PR debe facilitar su lectura:

### Plantilla de Pull Request:
```markdown
## Description
This PR resolves the database lock contention issue by introducing a serialized write mutex lock on our local SQLite engine and establishing an asynchronous retry with backoff.

## Key Architectural Decisions
- Used Python's standard `threading.Lock` to guarantee atomic transactional safety.
- Handled PostgreSQL sync delays by pushing raw transaction deltas as SSE (Server-Sent Events) backflows.

## How Was This Tested?
- [x] Unit test verified: `pytest tests/test_sqlite_locks.py`
- [x] Load tested: Handled 100 concurrent tasks over a 15-minute runtime without lock starvation.
```
