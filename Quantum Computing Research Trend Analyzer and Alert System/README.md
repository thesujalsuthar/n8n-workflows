# Quantum Computing Research Trend Analyzer and Alert System

## Overview
This workflow automates the process of fetching, analyzing, and reporting the latest research papers on quantum computing. It integrates data from arXiv and IEEE Xplore, extracts trending topics using NLP, summarizes key insights, and sends alerts via email and Slack.

---

## Workflow Nodes and Description

### 1. Fetch arXiv Papers
- **Type:** HTTP Request
- **Description:** Retrieves the latest 50 quantum computing research papers from arXiv.
- **API Endpoint:**  
  `http://export.arxiv.org/api/query?search_query=all:quantum+computing&start=0&max_results=50&sortBy=submittedDate&sortOrder=descending`
- **Response Format:** XML (as string)

---

### 2. Fetch IEEE Xplore Papers
- **Type:** HTTP Request
- **Description:** Fetches the latest 50 quantum computing papers from the IEEE Xplore digital library.
- **API Endpoint:**  
  `https://ieeexploreapi.ieee.org/api/v1/search/articles?querytext=quantum%20computing&max_records=50&sort_order=desc&apikey={{$credentials.IEEE_API.apiKey}}`
- **Authentication:** API Key in HTTP header.
- **Response Format:** JSON

---

### 3. Parse arXiv XML
- **Type:** Function
- **Description:** Parses the XML response from arXiv, extracting paper details including:
  - Title
  - Summary
  - Published date
  - Authors
  - Link to paper
- **Method:** Uses `xml2js` to convert XML to JSON objects.

---

### 4. Parse IEEE JSON
- **Type:** Function
- **Description:** Processes the JSON response from IEEE Xplore to extract key paper information:
  - Title
  - Summary (abstract)
  - Published date
  - Authors
  - Link to PDF or article page

---

### 5. Merge Paper Lists
- **Type:** Function
- **Description:** Combines the lists of papers from arXiv and IEEE, removing duplicates based on the paper title to form a unified set of unique research papers.

---

### 6. Analyze Trends with NLP
- **Type:** Function
- **Description:** Performs Natural Language Processing (NLP) on the merged paper list to identify the top 10 trending topics.
- **Details:**
  - Uses `compromise` NLP library.
  - Extracts nouns and noun-phrases from titles and summaries.
  - Counts frequency of each noun/noun-phrase.
  - Ranks and selects the top 10 trending topics.

---

### 7. Summarize Key Insights
- **Type:** Function
- **Description:** Generates a summary report highlighting each trending topic and listing up to three related research paper titles.
- **Output:** Text summary that maps trends to their associated papers.

---

### 8. Send Email Notification
- **Type:** Email Send
- **Description:** Sends the summarized trends report via email.
- **From:** `research-alerts@yourdomain.com`
- **To:** User's email (`userEmail` from input or credentials)
- **Subject:** Quantum Computing Research Trends Report
- **Body:** Contains the text summary from the previous node.
- **Credentials Required:** Email account credentials for sending email.

---

### 9. Send Slack Notification
- **Type:** Slack Message
- **Description:** Posts the summarized trends report to a designated Slack channel (`#quantum-research`).
- **Content:** The same textual summary sent via email.
- **Credentials Required:** Slack API credentials.

---

## Data Flow Summary

- The workflow begins by fetching research papers from both arXiv and IEEE Xplore APIs.
- It parses the raw data from both sources into a unified JSON format.
- Duplicate papers are removed, then NLP is applied to identify trending research topics.
- A textual summary mapping topics to papers is generated.
- The summary is sent as email and Slack notifications.

---

## Credentials Required

- **IEEE Xplore API Key:** For accessing IEEE Xplore articles.
- **Email Credentials:** For sending email notifications.
- **Slack API Credentials:** For posting messages in Slack channels.

---

## Usage Notes

- Replace placeholder credential IDs with your actual credential setups in the workflow environment.
- Ensure the email sending service and Slack app are configured with appropriate permissions.
- Adjust the user email or destination Slack channel as needed for notifications.
- Workflow can be scheduled or triggered to provide timely alerts on emerging quantum computing research trends.