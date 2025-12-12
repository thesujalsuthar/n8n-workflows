# Quantum Computing Research Trend Analyzer and Alert System

## Overview

This workflow automates the daily aggregation, analysis, and alerting of the latest research developments in the field of quantum computing. It fetches recent journal articles and preprints, processes and consolidates their content, uses AI to identify key research trends, and delivers summarized insights via email and Slack notifications.

---

## Workflow Components

### 1. Schedule Trigger
- **Type:** Schedule Trigger
- **Function:** Initiates the workflow once every 24 hours at UTC time.
- **Details:** Ensures that the data aggregation and analysis occur daily.

### 2. Fetch CrossRef Articles
- **Type:** HTTP Request
- **Endpoint:** `https://api.crossref.org/works`
- **Parameters:** Filters to get journal articles related to quantum computing, limited to the 50 most recent items.
- **Function:** Retrieves recent peer-reviewed journal articles on quantum computing.

### 3. Fetch arXiv Preprints
- **Type:** HTTP Request
- **Endpoint:** `https://api.arxiv.org/query`
- **Parameters:** Searches for the 50 most recent preprints related to quantum computing.
- **Function:** Pulls recent quantum computing preprints from arXiv.

### 4. Parse arXiv XML
- **Type:** Function Node
- **Function:** Parses the XML response from arXiv into structured JSON objects extracting:
  - Title
  - Authors
  - Summary
  - Publication date
- **Library Used:** `fast-xml-parser`

### 5. Transform CrossRef Data
- **Type:** Function Node
- **Function:** Normalizes CrossRef data into a clean JSON format extracting:
  - Title
  - Authors
  - Abstract
  - Published date
  - URL

### 6. Merge Papers
- **Type:** Merge Node
- **Mode:** Merge by index
- **Function:** Combines the output from CrossRef and arXiv into a single dataset.

### 7. Set Combined Text
- **Type:** Function Node
- **Function:** Concatenates the titles, authors, publication dates, abstracts, and summaries of all papers into one text string for AI analysis.

### 8. Analyze Trends with AI
- **Type:** OpenAI Node
- **Model:** GPT-4
- **Configuration:**
  - Temperature: 0.3
  - Max Tokens: 600
- **Prompt:** The AI assistant analyzes the combined text and provides a concise summary of key research trends, breakthroughs, and emerging topics, targeted at researchers and professionals.

### 9. Send Email Alert
- **Type:** Email Send Node
- **Function:** Sends the AI-generated research trend summary via email.
- **Recipients:** Dynamically set from the workflow's input or global user email.
- **Email Subject:** "Latest Quantum Computing Research Trends"
- **SMTP Credentials:** Required for email delivery.

### 10. Send Slack Alert
- **Type:** Slack Node
- **Function:** Posts the summarized research update to a Slack channel (`quantum-updates`).
- **Slack API Credentials:** Required for authentication and message posting.

---

## Data Flow Summary

1. Workflow triggers daily via **Schedule Trigger**.
2. Data fetched concurrently from:
   - **CrossRef Articles**
   - **arXiv Preprints**
3. Raw responses processed by:
   - **Transform CrossRef Data**
   - **Parse arXiv XML**
4. Both data streams merged in **Merge Papers**.
5. Combined content compiled in **Set Combined Text**.
6. AI model in **Analyze Trends with AI** interprets the text and generates a human-readable research summary.
7. Summary delivered via email (**Send Email Alert**) and Slack (**Send Slack Alert**) notifications.

---

## Setup Instructions

1. **OpenAI API:**
   - Obtain an API key and configure in the `Analyze Trends with AI` node credentials.

2. **SMTP Email Credentials:**
   - Configure SMTP server and credentials in the `Send Email Alert` node to enable email notifications.

3. **Slack API Credentials:**
   - Create and configure a Slack app with appropriate permissions.
   - Add token to `Send Slack Alert` node credentials.
   - Ensure the Slack channel `quantum-updates` exists.

4. **Timezone:**
   - Workflow runs daily at UTC timezone by default.

---

## Customization

- **Search Queries:** Adjust the query parameters for CrossRef or arXiv HTTP requests to expand or narrow the search scope.
- **Email Recipients:** Modify how the recipient email is sourced or hardcoded.
- **Slack Channel:** Change the Slack channel in the Slack node to fit your team’s needs.
- **AI Model and Prompt:** Tune the prompt or AI parameters to refine analysis style or depth.

---

## Summary

This automated workflow empowers researchers and professionals by delivering daily, AI-curated summaries of the latest quantum computing research, consolidating data from both peer-reviewed journals and preprints, and distributing insights through trusted communication channels.