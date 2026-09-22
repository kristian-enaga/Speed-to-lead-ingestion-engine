# 📌 Instant Speed-to-Lead Alert & CRM Ingestion

Real-time n8n pipeline that captures lead form submissions, enforces pre-flight schema validation, upserts contacts into HubSpot CRM with automated retry/DLQ failovers, and alerts sales on Slack within seconds—eliminating dropped leads and data corruption.

- **0-Second Latency:** Instant ingestion from webhook capture to CRM upsert and Slack alert.
- **Pre-flight Schema Gate:** Normalizes lead data, trims whitespace, lowercases emails, and validates email/name payloads prior to downstream routing.
- **Fault-Tolerant CRM Sync:** Direct HubSpot contact upsert with 3x auto-retries and Dead Letter Queue (DLQ) failover on 4xx/5xx API drops.
- **5-Minute Sales SLA:** Posts formatted urgency alerts to `#new-leads` with direct contact metadata.

**Stack:** n8n + Webhooks + HubSpot API + Google Sheets (DLQ & Quarantine) + Slack API

---

## 📹 Demo Walkthrough

Watch the 4-minute live walkthrough of the automated pipeline:
[Watch Automated CRM Lead Ingestion Demo on Loom](https://www.loom.com/share/707c78cf35df44a4adf3ee1a04d75b57)

![Workflow Architecture](./Speed-lead.png)

## 🎯 Business Problem

When a prospective client fills out a lead form, response speed dictates conversion. Studies show that waiting even 10 minutes drops closing rates by over 400%. Manual data entry, unvalidated contact payloads, and silent API drops cause corrupt CRM databases and lost pipeline revenue.

## 🚀 The Solution

An enterprise-grade, fault-tolerant lead processing pipeline built in n8n:

1. **Webhook Capture:** Ingests form submissions instantly with zero latency.
2. **Pre-flight Schema Gate (`NormalizeAndValidateLeadData`):** Cleans whitespace, lowercases emails, validates schema (`isValidEmail` & `isNamePresent`), and standardizes properties (`LeadName`, `LeadEmail`, `LeadPhone`, `LeadInquiry`, `processedAt`).
3. **Validation & Quarantine (`Validation: Email` / `No Email/Name`):** Routes valid payloads to production CRM sync; quarantine logs invalid schema leads with `SKIPPED_INVALID_SCHEMA` status for sales audit.
4. **HubSpot CRM Upsert (`Hubspot_Upsert Contact`):** Upserts contact records using `LeadEmail` as the unique lookup key. Configured with 3 retries (5s wait) and `On Error: Continue` error output routing.
5. **Dead Letter Queue (`DLQ_Backup_Log`):** Catches 4xx/5xx failures from HubSpot and Slack API endpoints, preserving contact context and logging execution metadata (`$json.node.name`, `$json.error.message`, `$now`, `$execution.id`).
6. **Instant Sales Urgency Alert (`SlackInstantAlert`):** Posts formatted Markdown alerts to `#new-leads` enforcing a target 5-minute sales response window.

## 🧪 Live Execution Proof & SLA Verification

Here is the verified execution log confirming instant lead ingestion, data normalization, database synchronization, and Slack alerting.

### 1. Successful n8n Ingestion Execution Log

![n8n Speed to Lead Execution History](./speed-to-lead-execution-history.png)
*Figure 1: Verified n8n execution history demonstrating 0-second ingestion latency across all pipeline nodes.*

## 💰 Business Impact & ROI

* **0-Second Lead Ingestion:** Eliminates response latency and secures maximum speed-to-lead conversion rates.
* **Zero Data Loss Architecture:** DLQ and quarantine logging guarantee no prospect payload is lost due to third-party API downtime or malformed user input.
* **100% CRM Hygiene:** Prevents duplicate and corrupt contact creation through strict normalization and email key matching.
