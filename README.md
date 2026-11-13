# 🛡️ FinGuard: Production Multi-Agent Expense & Invoice Triage System

[![CI/CD Pipeline](https://shields.io)]()
[![Python 3.11+](https://shields.io)]()
[![Architecture](https://shields.io)]()
[![Docker](https://shields.io)]()

**FinGuard** is a production-grade, governed multi-agent backend service that automates corporate invoice extraction, policy verification, and approval routing. Built using **Domain-Driven Design (DDD)**, **Test-Driven Development (TDD)**, and **deterministic financial guardrails**, it solves the core reliability, auditability, and cost challenges of deploying LLMs in financial workflows.

---

##  System Architecture

Unlike fragile notebook prototypes, FinGuard separates probabilistic LLM reasoning from deterministic business rules:

```text
[Invoice Upload (FastAPI)] 
       │
       ▼ (Idempotency Check via PostgreSQL)
[Event Bus / Domain Events]
       │
       ├──► 1. Extractor Agent (Structured Output / Pydantic Schema)
       ├──► 2. Policy & Budget Agent (RAG over Corporate Expense Rules)
       └──► 3. Deterministic Risk Gate (Hard-Coded Python Financial Rules)
                  │
                  ├──► Auto-Approve (< \$500 & Policy Compliant)
                  └──► Escalate to Human Approver (Audit Log Persisted)
```

### Key Production Engineering Features
* **Domain-Driven Design (DDD):** Clean separation between `domain/` (pure business rules), `application/` (agent orchestration), and `infrastructure/` (PostgreSQL, LLM providers, API routes).
* **Deterministic Financial Guardrails:** LLMs propose classifications and extract fields, while hard-coded Python domain policies enforce budget caps and vendor whitelists.
* **Idempotency & Audit Trails:** Every invoice hash and state transition is logged in PostgreSQL, preventing duplicate payouts on network retries and providing a full compliance audit trail.
* **Automated Evaluation Harness:** Includes a golden dataset evaluation pipeline (`evals/`) tracking field extraction F1-score, policy accuracy, p95 latency, and token cost per invoice.

---

##  Quickstart (Local Docker Deployment)

1. **Clone the repository**
   ```bash
   git clone https://github.com/oseipk/FinGuard
   cd finguard-invoice-agent
   ```

2. **Configure environment variables**
   ```bash
   cp .env.example .env
   # Add your OPENAI_API_KEY and POSTGRES_URL
   ```

3. **Spin up the API and PostgreSQL database**
   ```bash
   docker-compose up --build
   ```

4. **Run the TDD Unit Tests & AI Evaluation Suite**
   ```bash
   pytest tests/unit
   python -m evals.run_eval --dataset evals/golden_invoices.json
   ```

---

## Evaluation & Cost Benchmarks

| Metric | Target SLA | Current Benchmark |
| :--- | :--- | :--- |
| **Line-Item Extraction Accuracy** | > 98.0% | **99.1%** |
| **Policy Violation Catch Rate** | 100.0% | **100.0%** (Deterministic Gate) |
| **p95 End-to-End Latency** | < 3.5s | **2.1s** |
| **Average Cost per Invoice** | < $0.02 | **$0.008** (via Semantic Caching) |
