# Quantum Computing Research Trend Analyzer and Alert System

## Overview

This workflow automates the process of monitoring recent quantum computing research papers from prominent preprint repositories, analyzing key trends using AI, and sending personalized alerts to users based on their interests. It fetches papers from arXiv and bioRxiv, merges the data, performs AI-driven trend analysis, matches trends with user subscriptions, and sends alerts via email and Telegram.

---

## Workflow Nodes Description

### 1. Start
- Initiates the workflow.

---

### 2. Fetch bioRxiv quantum preprints
- Fetches the latest 20 quantum physics preprints from bioRxiv API.
- Endpoint: `https://api.biorxiv.org/details/quant-ph/0/20`

---

### 3. Fetch arXiv Quantum Computing Papers
- Fetches the last 20 updated quantum computing papers from arXiv API.
- Query URL:  
  `https://export.arxiv.org/api/query?search_query=all:quantum+computing&start=0&max_results=20&sortBy=lastUpdatedDate&sortOrder=descending`
- Uses a custom `User-Agent`: `"n8n-QuantumResearchBot/1.0"`

---

### 4. Parse bioRxiv JSON
- Parses the JSON response from bioRxiv.
- Extracts and formats:
  - Title
  - Authors (split by semicolon)
  - Published date
  - Summary (abstract)
  - Direct link to the bioRxiv paper

---

### 5. Parse arXiv XML
- Parses the XML response from arXiv using `xml2js`.
- Extracts and formats:
  - Title
  - Authors
  - Published date
  - Summary (abstract)
  - Link to the paper

---

### 6. Merge Papers
- Merges the parsed paper lists from bioRxiv and arXiv.
- Merging is done by paper title to avoid duplicates.
- Produces a consolidated list of recent quantum computing papers.

---

### 7. AI Trend Analysis
- Sends the consolidated list of paper titles and abstracts to an OpenAI GPT-4o-mini model.
- Prompt instructs the model to:
  - Identify key trends
  - Detect emerging topics and relevant keywords
  - Return a JSON array of the top 5 trends, each with:
    - A short description
    - Associated keywords
- Uses parameters:
  - Temperature: 0.7 (creativity level)
  - Max tokens: 1000 (response length limit)

---

### 8. Match Trends To User Interests
- Accesses a global list of user subscriptions containing their email, Telegram ID, and keyword preferences.
- Filters the AI-generated trends to find matches based on keywords.
- Creates alert objects for users with matched trends, including email, Telegram ID, and matched trends data.

---

### 9. Send Email Alerts
- Sends personalized email alerts to users with matched trends.
- Uses:
  - From: `alerts@quantumresearch.ai`
  - To: user email from matched trends
  - Subject: `Quantum Computing Research Alert: New Trends Detected`
  - Body: Lists matched trend descriptions and keywords, inviting users to stay informed.

---

### 10. Send Telegram Alerts
- Sends Telegram messages to users with matched trends.
- Uses:
  - Chat ID: user Telegram ID from matched trends
  - Text: List of matched trend descriptions and keywords.

---

## Data Flow Summary

- The **Start** node triggers fetching of bioRxiv and arXiv data in parallel.
- The fetched data is parsed and cleaned in respective parsing nodes.
- Both parsed results are merged into a single collection.
- The AI trend analysis node interprets trends from the combined dataset.
- User interests are matched with these trends.
- Alerts are sent via email and Telegram accordingly.

---

## User Subscriptions

- The workflow references a global variable `userSubscriptions`.
- This variable should be set in the global context with entries like:

```json
[
  {
    "email": "user@example.com",
    "telegramId": "123456789",
    "keywords": ["quantum machine learning", "error correction", "quantum algorithms"]
  },
  ...
]
```

- Keywords are used to match trends with user interests for targeted alerts.

---

## Requirements & Configuration

- **n8n** environment with:
  - HTTP Request nodes enabled
  - Function nodes enabled
  - OpenAI credential configured for GPT-4o-mini model
  - Email node configured with SMTP access for sending emails
  - Telegram node configured with a bot token for sending messages
- Proper global context variable setup for user subscriptions.

---

## Usage

1. Populate the `userSubscriptions` global context with users’ emails, Telegram IDs, and interest keywords.
2. Activate and run the workflow on a schedule to periodically fetch, analyze, and alert on new research trends.
3. Ensure email and Telegram integrations are correctly configured.
4. Review alert outputs and user feedback for tuning keyword lists and trends extraction.

---

# License

This workflow is free to use and modify. No warranty is provided.

---

For questions or support, please contact the maintainer.