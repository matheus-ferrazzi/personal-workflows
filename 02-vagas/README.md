# 🎯 Automated Talent Acquisition (n8n + PostgreSQL + AI)

> **Intelligent Web Scraper and Application Firewall (WAF) for IT Infrastructure opportunities.**

This repository hosts a pipeline for active IT Infrastructure job hunting. Acting as an automated Web Scraper, it uses a relational database to drop duplicate positions and an LLM configured as a WAF (Web Application Firewall) to analyze the adherence of a resume to the job requirements.

## 🧠 Workflows Breakdown

### 1. 🔍 Intelligent Job Scraper & WAF
**File:** `triagem de vagas.json`
A fully automated pipeline that fetches, filters, evaluates, and delivers highly-matched IT opportunities directly to your inbox.

* **API Ingress:** Consumes the JSearch API (RapidAPI) to fetch open positions based on strict geolocation parameters (e.g., São Paulo).
* **Sanitization & Deduplication:** Filters out empty payloads and upserts data into a **PostgreSQL** table to immediately drop already processed `job_id`s, saving API tokens and processing time.
* **AI Filter (WAF):** Passes new descriptions through a strict LLM prompt to compare requirements against the base resume. It drops false positives (e.g., fake remote jobs) and assigns a 0-100% adherence score.
* **Memory Buffer:** Caches approved positions in memory using a JavaScript node during the loop execution.
* **Daily Digest Consolidation:** Compiles the memory buffer into a unified, HTML-formatted report delivered via SMTP.

---

## 🛠️ Tech Stack & Methods

* **Orchestrator:** n8n (Self-hosted)
* **Databases:** PostgreSQL (State management & Deduplication)
* **Integrations:** RapidAPI (JSearch), SMTP (Gmail)
* **AI & LLMs:** Groq (Llama 3) for scoring and requirement analysis
* **Scripting:** JavaScript (Memory buffers, payload sanitization)
* **Security:**
    * Credential abstraction (No API keys exposed).
    * Safe SQL execution via parameter binding.
    * PII (Personal Identifiable Information) sanitized for public repository compliance.

---
*Maintained by [Matheus Ferrazzi](https://github.com/matheus-ferrazzi)*
