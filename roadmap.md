# Technical Roadmap

This document outlines the engineering milestones, optimization vectors, and structural enhancements planned for the platform. Our roadmap focuses strictly on scalability, operational resilience, and evaluation maturity.

---

## 🗺️ Engineering Milestones

### 🟢 Phase 1: Local Telemetry & Operational Resilience (Active)
- [ ] **Distributed Queue Recovery:** Implement an orphan-task recovery mechanism inside `Obrero Soberano` to handle hard crashes of physical nodes.
- [ ] **Local Model Keep-Alive Optimizations:** Implement automated CPU/GPU thread constraints and hotswap logic for Ollama model instance switches based on queue metrics.
- [ ] **OpenTelemetry Integration:** Establish standard OpenTelemetry tracing checkpoints in FastAPI middleware to monitor exact agent latencies.

### 🟡 Phase 2: AI Rigor & Evaluation Pipelines (Next)
- [ ] **Automated Evals (FinOps & Safety):** Build a automated evaluation script running as a CI/CD pipeline step (via GitHub Actions) checking for hallucination deltas, prompt injection mitigation, and API spend drift.
- [ ] **Semantic Guardrails Layer:** Integrate dynamic semantic sanitizers within the `Guard Agent` to drop inputs showing high cosine-similarity vectors to malicious prompts.
- [ ] **Distributed Cache Invalidation:** Implement an invalidation protocol for the Playwright scraper LRU local cache using Redis Pub/Sub channels.

### 🔴 Phase 3: RAG Edge Indexing & Advanced Telemetry (Backlog)
- [ ] **Localized Vector Storage Integration:** Evaluate SQLite-VSS versus PGVector for localized offline vector lookup on consumer-grade hardware.
- [ ] **Self-Healing Telemetry Actioners:** Extend the PowerShell daemon watchdog to trigger automatic environment rollbacks if local inference response latencies exceed a 2.5-second threshold.

---

## 📈 Devops & Metrics Maturity
Our progress is tracked through operational metrics. For live telemetry graphs, access the local Prometheus dashboard at `http://localhost:9090` or inspect the auto-generated Obsidian reports at `/config/telemetry/reports/`.
