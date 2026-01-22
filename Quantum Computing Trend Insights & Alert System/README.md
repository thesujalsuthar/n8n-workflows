# Quantum Computing Research Trend Analyzer and Alert System

## Overview

This workflow automates the process of collecting, analyzing, and alerting about the latest trends in quantum computing research. It fetches recent publications and news articles, performs natural language processing (NLP) analysis to identify emerging topics and overall sentiment, and sends email alerts with the summarized insights.

---

## Workflow Components

### 1. Trigger

- **Node:** `Trigger`
- **Type:** Interval
- **Function:** Initiates the workflow every hour (3600 seconds) to ensure updated data retrieval and processing.

---

### 2. Set Workflow Options

- **Node:** `Set Workflow Options`
- **Type:** Set
- **Purpose:** Defines configurable parameters such as:
  - `subfields`: List of quantum computing subfields to filter publications (default: `["quantum algorithms", "quantum hardware", "quantum error correction"]`).
  - `recipientEmail`: Email address to receive alerts (default: `research.team@example.com`).

---

### 3. Fetch Publications - CrossRef

- **Node:** `Fetch Publications - CrossRef`
- **Type:** HTTP Request
- **API:** CrossRef REST API
- **Function:** Retrieves the latest 100 research publications related to quantum computing, sorted by publication date in descending order.
- **Endpoint:**
  ```
  https://api.crossref.org/works?filter=subject:quantum%20computing&rows=100&sort=published&order=desc
  ```

---

### 4. Fetch News - NewsAPI

- **Node:** `Fetch News - NewsAPI`
- **Type:** HTTP Request
- **API:** NewsAPI
- **Function:** Fetches up to 100 recent news articles about quantum computing, sorted by publication date.
- **Headers:** Requires API key configured in credentials.
- **Endpoint:**
  ```
  https://newsapi.org/v2/everything?q=quantum%20computing&language=en&sortBy=publishedAt&pageSize=100
  ```

---

### 5. Parse Publications

- **Node:** `Parse Publications`
- **Type:** Function
- **Purpose:** Processes CrossRef API response and extracts:
  - Title
  - Abstract
  - Published date (formatted `YYYY-MM-DD`)
  - Authors (formatted as "FirstName LastName")
  - URL link to publication

---

### 6. Parse News Articles

- **Node:** `Parse News Articles`
- **Type:** Function
- **Purpose:** Processes NewsAPI response and extracts:
  - Title
  - Description
  - Content
  - Published date
  - Source name
  - URL link to article

---

### 7. Filter By Subfields

- **Node:** `Filter By Subfields`
- **Type:** Function
- **Purpose:** Filters parsed publications by checking if their titles contain any of the specified subfields from `subfields` option.

---

### 8. Combine Text for NLP

- **Node:** `Combine Text for NLP`
- **Type:** Function
- **Purpose:** Combines titles, abstracts, descriptions, and content from both filtered publications and news articles into a single text block suitable for NLP analysis.

---

### 9. NLP Emerging Topics & Sentiment

- **Node:** `NLP Emerging Topics & Sentiment`
- **Type:** OpenAI (text-davinci-003)
- **Purpose:** Analyzes combined text to identify:
  - Emerging research topics (list of strings)
  - Overall sentiment (`positive`, `negative`, or `neutral`)
- **Prompt:** Structured to return a JSON object with fields:
  - `topics`
  - `sentiment`

---

### 10. Parse NLP Response

- **Node:** `Parse NLP Response`
- **Type:** Function
- **Purpose:** Parses the raw response from OpenAI into a clean JSON object for downstream processing.

---

### 11. Send Email Alert

- **Node:** `Send Email Alert`
- **Type:** Email Send
- **Purpose:** Sends an email to the configured recipient with:
  - Subject: `"Quantum Computing Research Trends - <current date>"`
  - Plain text and HTML content summarizing:
    - The list of emerging topics
    - The overall sentiment from the NLP analysis
- **Recipient:** Uses either `recipientEmail` from workflow options or configured default email credentials.

---

## Configuration & Credentials

- **NewsAPI API Key:** Required and must be set in the credential named `NewsAPI`.
- **Email Credentials:** Configured for sending emails within n8n.
- **Default Email:** A fallback recipient email can be configured in credentials.

---

## Execution Flow Summary

1. The workflow is triggered on an hourly basis.
2. Workflow options are set for filters and recipient email.
3. Publications and news are fetched concurrently.
4. Publications are parsed and filtered by subfields; news articles are parsed.
5. Relevant texts are combined and sent to OpenAI for NLP analysis.
6. The response from OpenAI is parsed.
7. An email alert with the extracted emerging topics and sentiment is sent.

---

## Notes

- Adjust the `subfields` list in `Set Workflow Options` to tune the focus areas of interest.
- The frequency of the trigger node can be modified as needed.
- Ensure valid API keys and email configuration are set up for seamless execution.