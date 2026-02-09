# Upwork Automation Workflow

## Overview

This project implements and enhances an automated job-analysis pipeline built in **n8n** for evaluating Upwork listings relevant to automation and AI workflow development.

The workflow fetches job listings from the Apify API, pre-filters and structures them, uses an LLM to evaluate relevance, and stores qualified opportunities in Airtable. It also alerts when high-priority jobs are detected and logs additional observability metrics to improve traceability and future tuning.

The goal of this assignment was not just to run the provided workflow, but to make it stable, efficient, and production-oriented.

drive link of the demo video:  https://drive.google.com/file/d/1mvN_MZ-QaNQjsK7SihflJ41RZrra9Lp2/view?usp=sharing
---

## Workflow Architecture

High-level pipeline:

1. **Schedule Trigger**
   Runs automatically every 8 hours.

2. **HTTP Request Apify API**
   Fetches recent Upwork job listings.

3. **Edit Fields**
   Normalizes incoming data fields for downstream nodes.

4. **Pre-Filtering (If nodes)**
   Removes:

   * Entry-level jobs
   * Very low/unknown budget listings
     This reduces LLM token usage and improves scoring relevance.

5. **Analyse Job (OpenAI)**
   Scores jobs based on:

   * Automation relevance
   * Quick win potential
   * Portfolio value
   * Client credibility

   Produces structured JSON:

   ```
   score
   priority
   reason
   ```

6. **Observability Layer (Enhancement)**
   Logs additional signals:

   * automationSignal → keyword relevance score
   * budgetTier → Low / Medium / High classification
   * experienceCategory → normalized experience level
   * processedAt → execution timestamp

   This allows monitoring and future tuning of scoring behavior.

7. **Priority Branching**

   * **High Priority → Email Alert**
   * **All Results → Airtable Storage**

8. **Airtable Integration**
   Stores structured lead data for proposal readiness.

---

## Enhancements Implemented

### Email Alert for High-Priority Jobs

Implemented automated Gmail notification triggered when:

```
priority == High
```

The email includes:

* Job title
* Score
* Reasoning
* Direct link

This enables real-time opportunity tracking.

---

### Skill-Based Signal Extraction

Added keyword signal scoring to quantify automation relevance:

* n8n
* workflow
* AI
* Twilio / Retell
* integrations
* Zapier / Make

This metric supplements LLM scoring and provides transparency.

---

### Budget-Based Pre-Filtering

Jobs below a minimum budget threshold are filtered before LLM processing to:

* Save API cost
* Improve scoring quality
* Reduce noise

---

### Observability Logging

Added metrics stored alongside each record:

* Budget classification
* Automation signal strength
* Experience normalization
* Processing timestamps

This improves debugging, traceability, and future model tuning.

---

## Setup Instructions

### Requirements

* Docker (recommended)
* n8n
* Apify API token
* OpenAI API key
* Airtable credentials
* Gmail OAuth credential

---

### Run n8n (Docker)

```
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

   * Apify
   * OpenAI
   * Airtable
   * Gmail
4. Execute workflow

---

## AI Scoring Strategy

Jobs are evaluated for a candidate specializing in:

* n8n automation
* AI workflows
* system integrations
* conversational/voice automation

Scoring prioritizes:

* Fast completion potential
* Portfolio value
* Client legitimacy
* Automation alignment

This ensures high-value opportunities surface first.

---

## Repository Structure

```
workflow/
  Upwork-Automation.json

docs/
  demo-video.mp4

README.md
report.pdf
```

---

## Sample Output Fields

Stored in Airtable:

* Title
* Description
* URL
* Score
* Priority
* Reason
* Proposal placeholder
* Job timestamp
* Applied flag
* Observability signals

---

## Issues Identified & Fixed

* Missing token configuration
* OAuth credential errors
* Airtable type mismatches
* Expression mapping errors
* HTML parsing artifacts in job descriptions
* LLM output structure inconsistatches

---

## Future Improvements

* Slack/Discord alert channel
* Vector similarity job matching
* Historical scoring analytics
* Proposal auto-generation
* Reputation-weighted client scoring
* Retry + failure logging layer
* Rate-limit adaptive scheduling

---

## Demo

See repository demo video for:

* Full workflow execution
* AI scoring behavior
* Airtable population
* Alert trigger

---

## Author Notes

This implementation focused on moving beyond basic execution toward a realistic production mindset:

* Minimizing unnecessary model calls
* Introducing observability
* Improving decision transparency
* Building extensible workflow structure

The objective was to treat this as an engineering system rather than a simple automation exercise.
