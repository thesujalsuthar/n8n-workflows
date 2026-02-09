# Quantum Computing Research Trend Insight Aggregator and Alert System

## Overview

This workflow automates the process of collecting, analyzing, and alerting on the latest research trends in quantum computing. It aggregates data from journal articles, preprints, and news sources published within the last week, summarizes emerging trends using advanced AI, detects significant breakthroughs or changes, and sends real-time email alerts.

---

## Workflow Components

### 1. Cron Trigger
- **Type:** Cron
- **Description:** Schedules the workflow to run automatically at defined intervals (e.g., daily or weekly).
- **Position:** Start node triggering all data fetches in parallel.

### 2. Fetch Latest Journal Articles
- **Type:** HTTP Request
- **API Source:** [CrossRef API](https://api.crossref.org)
- **Parameters:**
  - Query: "quantum computing"
  - Filter: Articles published in the last 7 days
  - Rows: 20 (fetches top 20 relevant articles)
- **Description:** Retrieves the most recent journal articles regarding quantum computing from academic publications.

### 3. Fetch Latest Preprints
- **Type:** HTTP Request
- **API Source:** [bioRxiv API](https://api.biorxiv.org)
- **Parameters:**
  - Category: "quant-ph" (quantum physics)
  - Date Range: Last 7 days
- **Description:** Fetches recent preprints related to quantum physics, offering immediate access to cutting-edge unpublished research.

### 4. Fetch News Articles
- **Type:** HTTP Request
- **API Source:** [NewsAPI](https://newsapi.org)
- **Parameters:**
  - Query: "quantum computing"
  - From: Last 7 days
  - Sort By: Relevance
  - API Key: `YOUR_NEWSAPI_KEY` (replace with your API key)
- **Description:** Collects news articles reporting on developments, announcements, and popular coverage of quantum computing from global news outlets.

### 5. Aggregate Texts
- **Type:** Function
- **Functionality:**
  - Combines titles and abstracts/summaries from journal articles, preprints, and news articles into a unified list of text excerpts.
  - Prepares data for natural language processing.
- **Description:** Merges all gathered textual content to feed into an AI summarization model.

### 6. Summarize Trends Using ChatGPT
- **Type:** OpenAI Chat (gpt-3.5-turbo)
- **Parameters:**
  - Temperature: 0.5 (for balanced creativity and accuracy)
  - Max Tokens: 500 (length limit for response)
  - System Prompt: AI assistant specialized in summarizing scientific research trends.
  - User Prompt: Instructions to analyze and summarize emerging research trends, breakthroughs, and shifts in quantum computing over the past week.
- **Description:** Leverages OpenAI’s GPT model to generate a concise, insightful summary of the aggregated research and news content.

### 7. Detect Significant Trend Changes
- **Type:** Function
- **Functionality:**
  - Analyzes the AI-generated summary for keywords indicating significant announcements or breakthroughs.
  - Keywords include: "breakthrough", "novel", "significant", "first", "new", "improvement", "milestone", "demonstration", "advancement".
  - Triggers an alert if any keywords are found.
- **Description:** Automates detection of major developments in the summarized trends.

### 8. Send Real-Time Alert Email
- **Type:** Email Send
- **Parameters:**
  - From: `alerts@quantumresearchupdates.com`
  - To: `user@example.com` (replace with recipient email)
  - Subject: "Quantum Computing Research Update"
  - Body: Includes the summary and any detected alert keywords.
- **Description:** Sends a notification email containing the summary and highlights of significant developments directly to users.

---

## How It Works

1. **Scheduling:** The workflow is triggered on a schedule (e.g., daily/weekly) through the Cron Trigger.
2. **Data Fetching:** Simultaneously pulls the latest data on quantum computing from CrossRef, bioRxiv, and NewsAPI, focusing on the last 7 days.
3. **Aggregation:** Titles and abstracts/descriptions from all sources are combined for analysis.
4. **AI Summarization:** The aggregated texts are sent to OpenAI's GPT model to generate a clear, human-readable summary of current trends and breakthroughs.
5. **Trend Detection:** The summary is scanned for important keywords that signify potentially groundbreaking or newsworthy developments.
6. **Alerting:** If significant terms are detected, an alert email with the summary and keywords is dispatched to the configured recipients.

---

## Requirements & Setup

- **n8n Automation Platform:** This workflow is designed for n8n.
- **APIs and Keys:**
  - **NewsAPI:** Obtain an API key at https://newsapi.org and replace `YOUR_NEWSAPI_KEY` in the workflow.
- **Email Sending:**
  - Configure SMTP credentials in n8n’s Email node settings to enable email dispatch.
- **OpenAI API:** Ensure your OpenAI API key is configured and the OpenAI node has access.

---

## Customization

- **Email Recipients:** Change the `toEmail` value in the Email Send node to your preferred recipients.
- **Fetch Parameters:** Adjust query parameters (e.g., date ranges, number of articles) to fit your desired frequency and data volume.
- **Keyword List:** Modify the keywords in the "Detect Significant Trend Changes" node to tailor what triggers alerts.

---

## Summary

This workflow empowers researchers, analysts, and enthusiasts by providing:

- Timely aggregation of multiple research and news sources
- AI-generated summaries focusing on key developments
- Automated detection and notifications for major trend changes
- A customizable foundation for ongoing quantum computing insight monitoring

Deploy and run this system to stay ahead in the rapidly evolving field of quantum computing research.