# Upwork Automation Workflow

## Overview

This project implements and enhances an automated job-analysis pipeline built in **n8n** for evaluating Upwork listings relevant to automation and AI workflow development.

The workflow fetches job listings from the Apify API, pre-filters and structures them, uses an LLM to evaluate relevance, and stores qualified opportunities in Airtable. It also alerts when high-priority jobs are detected and logs observability metrics to improve traceability and future tuning.

The objective was not only to execute the provided workflow, but to make it stable, efficient, and production-oriented.

### Execution Validation

The workflow was executed end-to-end successfully without runtime errors.  
More than **10 job listings were processed and stored in Airtable** during validation testing, satisfying assignment requirements.

### Demo Video

https://drive.google.com/file/d/1mvN_MZ-QaNQjsK7SihflJ41RZrra9Lp2/view?usp=sharing

---

## Workflow Architecture

### High-Level Pipeline

1. **Schedule Trigger**  
   Runs automatically every 8 hours.

2. **HTTP Request (Apify API)**  
   Fetches recent Upwork job listings.

3. **Edit Fields**  
   Normalizes incoming data fields for downstream nodes.

4. **Pre-Filtering (If Nodes)**  
   Removes:
   - Entry-level jobs  
   - Very low or unknown budget listings  

   This reduces LLM token usage and improves scoring relevance.

5. **Analyse Job (OpenAI)**  
   Scores jobs based on:
   - Automation relevance  
   - Quick win potential  
   - Portfolio value  
   - Client credibility  

   Produces structured JSON:

   ```json
   {
     "score": "...",
     "priority": "...",
     "reason": "..."
   }
   ```

6. **Observability Layer (Enhancement)**  
   Logs additional signals:
   - `automationSignal` → keyword relevance score  
   - `budgetTier` → Low / Medium / High classification  
   - `experienceCategory` → normalized experience level  
   - `processedAt` → execution timestamp  

   These metrics enable monitoring, debugging, and future scoring refinement.

7. **Priority Branching**
   - High Priority → Email Alert  
   - All Results → Airtable Storage  

8. **Airtable Integration**  
   Stores structured lead data for proposal readiness.

---

## Enhancements Implemented

### Email Alert for High-Priority Jobs

Automated Gmail notification triggered when:

```text
priority == High
```

Email includes:
- Job title  
- Score  
- Reasoning  
- Direct link  

Enables real-time opportunity awareness.

---

### Skill-Based Signal Extraction

Keyword signal scoring quantifies automation relevance:

- n8n  
- workflow  
- AI  
- Twilio / Retell  
- integrations  
- Zapier / Make  

This supplements LLM scoring and improves transparency.

---

### Budget-Based Pre-Filtering

Jobs below a minimum threshold are filtered before LLM processing to:

- Reduce API costs  
- Improve scoring precision  
- Remove low-value noise  

---

### Observability Logging

Metrics stored alongside each record:

- Budget classification  
- Automation signal strength  
- Experience normalization  
- Processing timestamps  

Improves traceability and debugging capability.

---

## Setup Instructions

### Requirements

- Docker (recommended)
- n8n
- Apify API token
- OpenAI API key
- Airtable credentials
- Gmail OAuth credential

---

### Run n8n (Docker)

```bash
docker run -it --rm \
  -p 5678:5678 \
  -v ~/.n8n:/home/node/.n8n \
  n8nio/n8n
```

---

### Import Workflow

1. Open n8n  
2. Import `Upwork-Automation.json`  
3. Configure credentials:
   - Apify  
   - OpenAI  
   - Airtable  
   - Gmail  
4. Execute workflow  

---

### Credential Template

A configuration template is provided to illustrate required setup without exposing secrets.

Users must supply:

- Apify API Token  
- OpenAI API Key  
- Airtable Credentials  
- Gmail OAuth Access  

No sensitive information is stored in this repository.

---

## AI Scoring Strategy

Jobs are evaluated for a candidate specializing in:

- n8n automation  
- AI workflows  
- system integrations  
- conversational / voice automation  

Scoring prioritizes:

- Fast completion potential  
- Portfolio value  
- Client legitimacy  
- Automation alignment  

This ensures relevant opportunities surface first.

---

## Repository Structure

```
workflow/
  Upwork-Automation.json

docs/
  Setup.md
  Report.md

screenshots/
  AirTable1.png
  AirTable2.png
  WorkFlow.png

demo/
  demo-video-link.txt

README.md
```

---

## Sample Output Fields

Stored in Airtable:

- Title  
- Description  
- URL  
- Score  
- Priority  
- Reason  
- Proposal placeholder  
- Job timestamp  
- Applied flag  
- Observability signals  

---

## Issues Identified & Fixed

- Missing token configuration  
- OAuth credential errors  
- Airtable type mismatches  
- Expression mapping errors  
- HTML parsing artifacts in job descriptions  
- LLM output structure inconsistencies  

---

## Future Improvements

- Slack/Discord alert channel  
- Vector similarity job matching  
- Historical scoring analytics  
- Proposal auto-generation  
- Reputation-weighted client scoring  
- Retry and failure logging layer  
- Rate-limit adaptive scheduling  

---

## Demo

Repository demo illustrates:

- Full workflow execution  
- AI scoring behavior  
- Airtable population  
- Alert triggering  

---

## Author Notes

This implementation focused on moving beyond basic execution toward a production-oriented engineering mindset:

- Minimizing unnecessary model calls  
- Introducing observability  
- Improving decision transparency  
- Building extensible workflow structure  

The goal was to treat the system as an engineering workflow rather than a simple automation exercise.
