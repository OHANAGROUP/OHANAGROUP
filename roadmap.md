# Technical Roadmap

This document outlines the engineering milestones, optimization vectors, and structural enhancements planned for the platform. Our roadmap focuses strictly on scalability, operational resilience, and evaluation maturity.

---

## 🗺️ Engineering Milestones

### 🟢 Phase 1: Local Telemetry & Operational Resilience (Active)
- [ ] **Distributed Queue Recovery:** Implement an orphan-task recovery mechanism inside the core worker loop to handle worker or host crashes.
- [ ] **Local Model Keep-Alive Optimizations:** Implement automated CPU thread constraints and model state hotswapping for Ollama instances based on active queue throughput.
- [ ] **OpenTelemetry Integration:** Establish standard OpenTelemetry tracing checkpoints in FastAPI middleware to monitor exact task processing latencies.

### 🟡 Phase 2: AI Rigor & Evaluation Pipelines (Next)
- [ ] **Automated Evals (FinOps & Safety):** Build an automated evaluation script running as a CI/CD step (via GitHub Actions) checking for hallucination deltas, prompt injection mitigation, and API spend drift.
- [ ] **Semantic Guardrails Layer:** Integrate dynamic semantic sanitizers within the validation engine to drop inputs semantically similar to known prompt injection patterns.
- [ ] **Distributed Cache Invalidation:** Implement a lightweight invalidation protocol for the local Playwright scraper LRU cache using Redis Pub/Sub.

### 🔴 Phase 3: Edge Indexing & Advanced Telemetry (Backlog)
- [ ] **Edge Vector Storage Evaluation:** Evaluate SQLite-VSS versus PGVector for offline vector search on resource-constrained hardware constraints.
  
  **Trade-off Focus:**
  - *Operational simplicity:* Single file SQLite dependency versus hosting separate database runtimes.
  - *Memory overhead:* Restricting RAM utilization of vector indexes during active local inference sweeps.
  - *WAL Contention:* Evaluating write lock contention under rapid parallel queue writes.
  - *Portability:* Ease of deployment and distribution across low-power edge hardware.

- [ ] **Self-Healing Telemetry Actions:** Extend the PowerShell OS watchdog daemon to trigger automatic queue scaling or process restarts if engine latency parameters are breached.

---

## 📈 DevOps & Metrics Maturity
Our progress is tracked through operational metrics. For live telemetry, query the local Prometheus dashboard at `http://localhost:9090` or inspect the auto-generated reports located under `/config/telemetry/reports/`.
