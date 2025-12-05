# Quantum Computing Research Paper Aggregator and Summary Generator

## Overview

This workflow automatically fetches the latest research papers on quantum computing from arXiv and IEEE Xplore, processes and summarizes them using an AI model, and distributes the summaries via email and Slack. It is designed to run daily on weekdays to keep you updated with recent developments in the field of quantum computing.

---

## Workflow Components

### 1. Daily Trigger

- **Node:** `Daily Trigger`
- **Type:** Cron
- **Purpose:** Triggers the workflow every weekday at 8:00 AM.

---

### 2. Fetch arXiv Papers

- **Node:** `Fetch arXiv Papers`
- **Type:** HTTP Request
- **API:** arXiv API (`http://export.arxiv.org/api/query`)
- **Query Parameters:**
  - `search_query`: `all:quantum computing`
  - `start`: `0`
  - `max_results`: `20`
  - `sortBy`: `lastUpdatedDate`
  - `sortOrder`: `descending`
- **Purpose:** Retrieves the 20 most recent quantum computing papers from arXiv.

---

### 3. Fetch IEEE Xplore Papers

- **Node:** `Fetch IEEE Xplore Papers`
- **Type:** HTTP Request
- **API:** IEEE Xplore API (`https://ieeexploreapi.ieee.org/api/v1/search/articles`)
- **Query Parameters:**
  - `querytext`: `quantum computing`
  - `max_records`: `20`
  - `sort_order`: `desc`
  - `sort_field`: `publication_year`
- **Authentication:** Uses predefined IEEE Xplore API credentials.
- **Purpose:** Retrieves the 20 most recent quantum computing papers from IEEE Xplore.

---

### 4. Parse arXiv Response

- **Node:** `Parse arXiv Response`
- **Type:** Function
- **Code:** Parses XML Atom feed from arXiv.
- **Output:** JSON array of paper objects, each containing:
  - `id`
  - `title`
  - `abstract`
  - `published` date
  - `authors`
- **Purpose:** Converts arXiv XML data into usable JSON format.

---

### 5. Parse IEEE Response

- **Node:** `Parse IEEE Response`
- **Type:** Function
- **Code:** Extracts relevant information from IEEE Xplore JSON response.
- **Output:** JSON array of paper objects, each containing:
  - `id`
  - `title`
  - `abstract`
  - `published` year
  - `authors`
- **Purpose:** Converts IEEE Xplore API data into a unified JSON structure.

---

### 6. Combine Papers

- **Node:** `Combine Papers`
- **Type:** Function
- **Code:** Merges arXiv and IEEE paper arrays into a single list.
- **Purpose:** Consolidates all collected papers for summarization.

---

### 7. Summarize Papers

- **Node:** `Summarize Papers`
- **Type:** OpenAI (GPT-4o-mini)
- **Operation:** Chat completion
- **Prompt:**
  - System role: Assistant specialized in summarizing research papers.
  - User role: Summarizes each paper’s abstract and highlights key findings.
- **Purpose:** Generates a clear and concise summary of each research paper.

---

### 8. Generate Report

- **Node:** `Generate Report`
- **Type:** Function
- **Code:** Aggregates individual paper summaries into a formatted report string.
- **Output:** Single report containing all paper summaries with titles and summaries separated by dividers.

---

### 9. Send Email

- **Node:** `Send Email`
- **Type:** Email Send
- **Parameters:**
  - `fromEmail`: Your email address (e.g., `your-email@example.com`)
  - `toEmail`: Recipient(s) email address (e.g., `recipient@example.com`)
  - `subject`: `"Quantum Computing Research Papers Summary"`
  - `text`: The summarized report text.
- **Purpose:** Sends the compiled summary report via email.

---

### 10. Send Slack Message

- **Node:** `Send Slack Message`
- **Type:** Slack
- **Parameters:**
  - `channel`: Your Slack channel ID
  - `text`: The summarized report text.
- **Purpose:** Sends the compiled summary report as a message to a Slack channel.

---

## Execution Flow

1. **Daily Trigger** initiates the workflow every weekday at 08:00 AM.
2. Parallel HTTP requests are made to **arXiv** and **IEEE Xplore** APIs to fetch recent quantum computing papers.
3. Responses are parsed separately using **Parse arXiv Response** and **Parse IEEE Response** nodes.
4. Parsed paper lists are merged in **Combine Papers**.
5. Each paper is sent to the **Summarize Papers** node for AI-generated summaries.
6. All summaries are combined into a single report in **Generate Report**.
7. The report is simultaneously sent via **Send Email** and **Send Slack Message** nodes.

---

## Prerequisites

- n8n instance set up and running.
- Access and credentials for IEEE Xplore API.
- OpenAI API key with access to GPT-4o-mini or compatible model.
- Email account configured in n8n for sending emails.
- Slack app set up with required permissions and token for messaging.

---

## Configuration

- Replace email addresses in **Send Email** node with valid sender and recipient emails.
- Set your authorized Slack channel ID in **Send Slack Message** node.
- Configure credentials for:
  - IEEE Xplore API
  - OpenAI API
  - Email provider
  - Slack app
- Adjust the cron schedule in **Daily Trigger** as required.

---

## Summary

This workflow automates the collection, parsing, summarization, and distribution of the latest quantum computing research papers, streamlining research updates by email and Slack every weekday morning.