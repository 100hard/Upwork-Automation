# Setup Guide  
## Upwork Automation Workflow

This document explains how to reproduce and run the automation workflow locally using Docker and n8n.

---

## System Overview

The system:

- Scrapes Upwork jobs using **Apify**
- Filters and enriches listings
- Scores relevance using an **LLM**
- Sends alerts for high-priority jobs
- Stores structured data in **Airtable**

---

## Prerequisites

Install the following:

- Docker Desktop
- Git
- A modern browser

### Accounts / API Keys

- OpenAI API key
- Apify API token
- Airtable account + API key
- Gmail OAuth credentials *(optional — alerts)*

---

## Repository Structure

```bash
upwork-automation/
├─ workflow/
│  ├─ docker-compose.yml
│  ├─ .env
│  └─ Upwork Automation.json
├─ docs/
├─ screenshots/
└─ demo/
```

---

## 1. Start n8n

Navigate to the workflow directory:

```bash
cd workflow
```

Start the container:

```bash
docker compose up -d
```

This launches n8n at:

```
http://localhost:5678
```

Login using credentials defined in `docker-compose.yml`.

---

## 2. Configure Environment Variables

Edit `.env`:

```env
OPENAI_API_KEY=your_key
APIFY_TOKEN=your_token
AIRTABLE_TOKEN=your_token
AIRTABLE_BASE_ID=your_base
```

Restart container after changes:

```bash
docker compose restart
```

---

## 3. Import Workflow

Open n8n UI

Go to:

```
Workflows → Import
```

Upload:

```
Upwork Automation.json
```

---

## 4. Configure Credentials in n8n

Inside n8n configure:

### OpenAI
- Paste API key

### Apify
- Add token

### Airtable
- Add Personal Access Token
- Select Base + Table

### Gmail (Optional)
- OAuth setup for alert notifications

---

## 5. Run Workflow

### Manual Run
```text
Execute Workflow
```

### Automatic Scheduling
- Trigger runs every **8 hours**
- Fetches jobs posted within last **6 hours**

---

## 6. Expected Output

### Gmail Alert
High priority jobs generate email notifications.

### Airtable Records
Each listing stores:

- Title  
- Description  
- Score  
- Priority  
- Reason  
- Observability signals  
- Timestamp  

---

## Troubleshooting

### Container Not Starting

```bash
docker compose down
docker compose up --build
```

---

### Port Conflict

Change port mapping in:

```
docker-compose.yml
```

Example:

```yaml
- "5679:5678"
```

---

### Credential Errors

Verify:

- Tokens are valid
- OAuth redirect URL matches
- Services are enabled

---

## Reproducibility Notes

This setup was designed for:

- Minimal manual configuration
- Clear credential separation
- Deterministic workflow execution
- Easy local deployment

The entire system can be rebuilt from this repository in under **5 minutes**.

---

## Contact

For questions regarding workflow behavior or configuration:

- Refer to project documentation in `/docs`
- Inspect workflow nodes inside n8n
