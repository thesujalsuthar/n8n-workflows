# Quantum Computing Research Trend Analyzer and Alert System

## Overview

This workflow automates the process of gathering, analyzing, and delivering curated insights on the latest quantum computing research. Leveraging multiple academic APIs and AI-driven trend analysis, it identifies emerging developments and sends personalized alerts to subscribed researchers.

---

## Workflow Components

### 1. Periodic Trigger

- **Type:** Cron Trigger
- **Schedule:** Runs at 9:00 AM every Monday, Wednesday, and Friday.
- **Function:** Initiates the workflow to fetch and process new research papers regularly.

---

### 2. Fetching Research Papers

The workflow collects recent papers related to quantum computing from three primary sources:

- **Fetch Crossref Papers**
  - Fetches papers published since the previous day.
  - Uses Crossref API with query: `"quantum computing"`.
  - Retrieves up to 20 papers in JSON format.

- **Fetch arXiv Papers**
  - Queries arXiv database with the same research topic.
  - Fetches up to 20 recent papers.
  - Requires an arXiv API key (`<YOUR_ARXIV_API_KEY>`).

- **Fetch IEEE Papers**
  - Searches IEEE database for papers matching "quantum computing".
  - Fetches up to 20 recent papers.
  
---

### 3. Normalize and Combine Data

- **Purpose:** Converts heterogeneous data structures from Crossref, arXiv, and IEEE into a unified format.
- **Output Fields:**
  - `id` – Unique identifier or DOI.
  - `title` – Paper title.
  - `authors` – Comma-separated list of authors.
  - `abstract` – Abstract text with HTML tags removed.
  - `publishedDate` – Publication date.
  - `source` – Origin of data (Crossref, arXiv, IEEE).
  - `url` – Link to the paper or PDF.

---

### 4. AI Trend Analysis

- **Model:** OpenAI GPT-4.
- **Input:** Concatenated abstracts of all fetched papers.
- **Task:** Analyzes abstracts to detect emerging trends, keywords, significant developments, and potential research opportunities within quantum computing.
- **Temperature:** 0.3 for focused, informative summaries.
- **System Role:** Expert AI assistant in research analysis.

---

### 5. Apply Custom Filters

- **Function:** Filters the combined list of papers based on user-defined criteria.
- **Current Filter:** Filters by subtopic keywords appearing in paper titles or abstracts.
- **Input:** User-provided filters (e.g., specific quantum computing subfields).

---

### 6. User Subscription Data

- **Data Set:** Contains subscriber email and filter preferences.
- **User Inputs:**
  - `email` – Recipient address for alerts.
  - `filters` – JSON with optional subtopic filter (default is empty).

---

### 7. Prepare Notification Content

- **Actions:**
  - Aggregates all abstracts into a single string.
  - Extracts AI-generated trend summary.
  - Gathers filtered papers matching user preferences.
  - Retrieves subscriber email.
- **Output:** Prepares all data needed for the email content.

---

### 8. Send Email Alerts

- **Sender Email:** Replace `<YOUR_SENDER_EMAIL>` with your verified sending email.
- **Recipient:** User email (`userEmail`) from subscription data.
- **Subject:** `"Quantum Computing Research Update: Emerging Trends and Opportunities"`
- **Body:**
  ```
  Dear Researcher,

  Here are the latest trends and research opportunities in quantum computing based on recent papers:

  {{trendSummary}}

  Filtered Papers of Interest:

  - Title by Authors (Publication Date)
  URL: Paper URL

  Best regards,
  Quantum Computing Research Alert System
  ```

---

## Configuration Instructions

- **API Keys & Emails**
  - Insert your arXiv API key in the `Fetch arXiv Papers` node.
  - Specify your sender email in the `Send Email Alerts` node.
  
- **User Subscription**
  - Users must supply their email and optional filters (such as specific subtopics).
  
- **Scheduling**
  - Adjust the `Periodic Trigger` node to change frequency or timing.

---

## Summary

This workflow provides a fully automated pipeline that:

- Regularly fetches the latest quantum computing research papers.
- Normalizes diverse data sources for uniform processing.
- Employs GPT-4 to detect and summarize emerging trends.
- Filters papers based on user interest.
- Sends personalized email alerts highlighting current research developments and opportunities.

Use this system to stay informed and ahead in the fast-evolving field of quantum computing research.