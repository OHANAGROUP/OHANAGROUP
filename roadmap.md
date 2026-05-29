# Technical Roadmap

This document outlines the engineering milestones, optimization vectors, and structural enhancements planned for the platform. Our roadmap focuses strictly on scalability, operational resilience, and evaluation maturity.

---

## 🗺️ Engineering Milestones

### 🟢 Phase 1: Local Telemetry & Operational Resilience (Active)
- [ ] **Distributed Queue Recovery:** Implement an orphan-task recovery mechanism inside the core worker loop to handle hard crashes of physical nodes.
- [ ] **Local Model Keep-Alive Optimizations:** Implement automated CPU thread constraints and model state hotswapping for Ollama instances based on active queue throughput.
- [ ] **OpenTelemetry Integration:** Establish standard OpenTelemetry tracing checkpoints in FastAPI middleware to monitor exact task processing latencies.

### 🟡 Phase 2: AI Rigor & Evaluation Pipelines (Next)
- [ ] **Automated Evals (FinOps & Safety):** Build an automated evaluation script running as a CI/CD step (via GitHub Actions) checking for hallucination deltas, prompt injection mitigation, and API spend drift.
- [ ] **Semantic Guardrails Layer:** Integrate dynamic semantic sanitizers within the validation engine to drop inputs showing high cosine-similarity vectors to known malicious prompt patterns.
- [ ] **Distributed Cache Invalidation:** Implement a lightweight invalidation protocol for the local Playwright scraper LRU cache using Redis Pub/Sub.

### 🔴 Phase 3: Edge Indexing & Advanced Telemetry (Backlog)
- [ ] **Localized Vector Storage Integration:** Evaluate SQLite-VSS versus PGVector for localized offline vector lookups on consumer-grade hardware constraints.
- [ ] **Self-Healing Telemetry Actions:** Extend the PowerShell OS watchdog daemon to trigger automatic queue scaling or process restarts if engine latency parameters are breached.

---

## 📈 DevOps & Metrics Maturity
Our progress is tracked through operational metrics. For live telemetry, query the local Prometheus dashboard at `http://localhost:9090` or inspect the auto-generated reports located under `/config/telemetry/reports/`.
