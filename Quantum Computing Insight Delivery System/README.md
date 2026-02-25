# Quantum Computing Trend Analyzer Workflow

## Overview
This workflow automates the process of fetching the latest research publications and news articles related to quantum computing, analyzes emerging trends using AI, and delivers summarized insights to subscribers via email and Slack.

## Workflow Nodes Description

### 1. Scheduler
- **Type:** `Schedule Trigger`
- **Function:** Triggers the workflow daily at 8:00 AM to ensure timely updates.
- **Configuration:** Set to trigger once per day at 08:00 hours.

### 2. Fetch Research Publications
- **Type:** `HTTP Request`
- **Function:** Queries a research publication database (e.g., PubMed or a similar API) to retrieve the newest 50 articles related to "quantum computing".
- **Parameters:**
  - Resource: `article`
  - Operation: `getMany`
  - Query: `"quantum computing"`, sorted by newest, 50 items per page.
  - Authentication: Uses an API key stored in credentials (`pubmedApi.apiKey`).

### 3. Fetch Quantum Computing News
- **Type:** `HTTP Request`
- **Function:** Fetches news articles from NewsAPI.org related to quantum computing.
- **Parameters:**
  - URL: Requests the latest 50 news articles sorted by publication date.
  - Header: Authorization token (`newsApi.key`) from credentials.

### 4. Merge Publications & News
- **Type:** `Merge`
- **Function:** Combines data outputs from the research publications and news fetch nodes.
- **Configuration:** Merges both datasets by the key `id` to consolidate articles and news items.

### 5. Normalize Data
- **Type:** `Function`
- **Function:** Processes and normalizes the merged data to a uniform format including:
  - Title
  - Description (or summary)
  - Publication date
  - Source
  - URL
- **Purpose:** Ensures consistent data structure for analysis.

### 6. AI Trend Analysis
- **Type:** `OpenAI (text-davinci-003)`
- **Function:** Analyzes the normalized articles and news items to identify emerging trends, frequent topics, sentiment, key research focuses, breakthroughs, and notable shifts within quantum computing.
- **Prompt:** Includes the list of items’ titles and descriptions, requesting a structured summary.

### 7. Load Subscribers
- **Type:** `Function`
- **Function:** Loads subscriber information including:
  - Email address: `researcher@example.com`
  - Slack channel: `#quantum-alerts`
- **Purpose:** Provides recipient information for alert dispatching.

### 8. Send Email Alerts
- **Type:** `Email Send`
- **Function:** Sends a daily email with the AI-generated summary of quantum computing trends.
- **Parameters:**
  - Recipient: Email loaded from "Load Subscribers" node.
  - Subject: "Quantum Computing Research Trend Summary - [Current Date]"
  - Body: AI-generated summary text.

### 9. Send Slack Alerts
- **Type:** `Slack`
- **Function:** Posts the daily trend summary to a specified Slack channel for real-time alerting.
- **Parameters:**
  - Channel: Slack channel loaded from "Load Subscribers" node.
  - Message: Includes the date and AI-generated summary.

## Workflow Connections
- The **Scheduler** triggers both data-fetch nodes in parallel.
- Outputs from **Fetch Research Publications** and **Fetch Quantum Computing News** merge to form a unified data set.
- Merged data undergoes normalization before AI analysis.
- The summarized insights are then disseminated to subscribers via Email and Slack through the loaded subscriber information.

## Credentials Required
- **PubMed API Key** (`pubmedApi.apiKey`): For accessing research publications.
- **News API Key** (`newsApi.key`): For accessing latest news articles.
- **Slack API Token:** For posting messages to Slack.
- **Email SMTP credentials:** For sending emails.

---

This automated workflow ensures stakeholders receive concise, up-to-date summaries of important developments in quantum computing daily through preferred communication channels.