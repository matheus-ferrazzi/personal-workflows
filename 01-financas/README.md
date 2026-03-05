# 💰 FinOps & Lifecycle Automation (n8n + AI + Sheets)

> **Personal engineering-grade ecosystem for FinOps, Observability, and Disaster Recovery.**

This repository hosts a collection of advanced workflows designed to orchestrate personal financial operations. By leveraging **n8n** as the central nervous system, **Groq AI** for natural language processing, and **Google Sheets** as the data lake, this project automates expense tracking, systemizes health checks, and ensures high availability of financial data.

## 🧠 Workflows Breakdown

### 1. 🤖 AI-Driven Expense Tracker (Ingestion & Query)
**File:** `Controle financeiro.json`
An NLP-driven pipeline that replaces manual spreadsheet data entry with natural language processing via Telegram.

* **LLM Parsing:** Uses an AI Agent to extract structured data (Amount, Category, Date, Installments) from casual Telegram voice or text messages.
* **Data Sanitization:** Applies Regex and JavaScript formatting to ensure data compliance before database insertion.
* **Data Lake Integration:** Executes secure `append` operations directly into a Google Sheets database.
* **Query Routing:** Recognizes intent to query balances and uses the LLM to summarize recent rows on demand.

### 2. 💓 Financial Heartbeat (Observability)
**File:** `Heart Check.json`
A proactive observability routine to monitor financial health without requiring manual dashboard checks.

* **Scheduled Cron:** Runs automatically 3x a week.
* **Trend Analysis:** Queries recent rows from the database and uses an LLM to generate a summary of the weekly burn rate and spending habits.
* **Alerting:** Delivers a consolidated health check report back to a secure Telegram chat.

### 3. 🚑 Disaster Recovery (Data Backup)
**File:** `BKP da planiha.json`
Ensures high availability and prevents data loss for the personal financial database.

* **Automated Export:** Triggers monthly to securely authenticate via OAuth2 and download the current state of the Google Sheet as an `.xlsx` physical file.
* **Secure Vault:** Sends the binary file directly to a private Telegram channel acting as an encrypted cold storage vault.

---

## 🛠️ Tech Stack & Methods

* **Orchestrator:** n8n (Self-hosted)
* **Databases:** Google Sheets API (Data Lake)
* **Integrations:** Telegram API, Google Drive API
* **AI & LLMs:** Groq (Llama 3) for NLP and classification
* **Scripting:** JavaScript (Regex, array manipulation, data normalization)
* **Security:**
    * Credential abstraction (No hardcoded passwords).
    * OAuth2 / Service Account implementation.

---
*Maintained by [Matheus Ferrazzi](https://github.com/matheus-ferrazzi)*
