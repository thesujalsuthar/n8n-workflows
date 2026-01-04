# Quantum Computing Research Trend Analyzer and Alert System

## Overview

This workflow automates the collection, aggregation, and alerting of the latest trends and developments in quantum computing research using multiple data sources. It fetches recent academic papers, scholarly articles, and news updates, consolidates the data, generates insights (placeholder), and sends email alerts to subscribers.

---

## Workflow Nodes and Description

### 1. HTTP Request - arXiv
- **Purpose:** Fetches the 5 most recent papers related to quantum computing from the arXiv API.
- **Request URL:**  
  `http://export.arxiv.org/api/query?search_query=all:quantum+computing&start=0&max_results=5&sortBy=submittedDate&sortOrder=descending`
- **Response Format:** String (XML)
- **Outputs:** Raw XML data about recent quantum computing papers.

### 2. Parse arXiv XML
- **Purpose:** Parses the XML response from arXiv to extract relevant fields.
- **Process:**  
  - Uses regex parsing (due to Node environment limitations).  
  - Extracts `title`, `summary`, `published date`, and `link` for each paper.
- **Outputs:** Cleaned array of paper data objects.

### 3. HTTP Request - Google Scholar
- **Purpose:** Fetches search results related to quantum computing from Google Scholar via SerpAPI.
- **Request URL:**  
  `https://serpapi.com/search.json?q=quantum+computing&engine=google_scholar&api_key={{ $credentials.serpApiApiKey.apiKey }}`
- **Response Format:** JSON
- **Authentication:** API key stored in credentials
- **Outputs:** Raw JSON containing organic results (papers/articles).

### 4. Extract Google Scholar Data
- **Purpose:** Extracts up to 5 relevant entries from the Google Scholar results.
- **Fields Extracted:** `title`, `snippet` (summary), `link`
- **Outputs:** Simplified list of scholarly articles.

### 5. HTTP Request - NewsAPI
- **Purpose:** Retrieves latest news articles regarding quantum computing.
- **Request URL:**  
  `https://newsapi.org/v2/everything?q=quantum+computing&sortBy=publishedAt&pageSize=5&apiKey={{ $credentials.newsApiKey.apiKey }}`
- **Response Format:** JSON
- **Authentication:** API key in header
- **Outputs:** Raw JSON news data.

### 6. Extract NewsAPI Data
- **Purpose:** Parses and extracts the important fields from NewsAPI response.
- **Fields Extracted:** `title`, `description`, `url`, `publishedAt`
- **Outputs:** List of news articles related to quantum computing.

### 7. Aggregate All Data
- **Purpose:** Collects parsed data from arXiv, Google Scholar, and NewsAPI and merges them into a single unified list.
- **Additional Fields:** Adds a `source` tag to distinguish data origin (`arXiv`, `Google Scholar`, or `News`).
- **Outputs:** Unified dataset of papers and articles for further processing.

### 8. Set Subscribers
- **Purpose:** Defines subscriber email addresses who will receive alerts.
- **Data:**  
  - `emailAddresses`: `"user1@example.com,user2@example.com"`
- **Outputs:** Email list for alert dispatch.

### 9. Placeholder NLP - Summarization
- **Purpose:** Placeholder node intended for advanced NLP processing.  
- **Notes:** Replace this node with an actual NLP integration (e.g., text summarization or trend analysis API) to generate summarized insights based on aggregated data.
- **Outputs:** Summary or default alert text.

### 10. Send Email Alerts
- **Purpose:** Sends an email notification to all subscribers containing the latest quantum computing research and news updates or summarized insights.
- **Fields:**  
  - **From:** `alerts@quantumhub.example`  
  - **To:** Emails from the "Set Subscribers" node  
  - **Subject:** `Quantum Computing Trend Alert`  
  - **Body Text:** Content from the NLP summarization node or fallback message
- **Output:** Email delivery status.

---

## How It Works (Flow)

1. Fetch recent quantum computing papers and articles from three sources:
   - arXiv (XML format)
   - Google Scholar (via SerpAPI)
   - News articles (via NewsAPI)
2. Parse and extract the relevant information from each API response.
3. Aggregate all collected data into a single data structure with source tags.
4. Prepare a list of subscriber emails.
5. Generate summarization and insights from the aggregated data (currently a placeholder function).
6. Send an email alert with latest findings and trends to the subscribers.

---

## Credentials Required

- **SerpAPI API Key** (`serpApiApiKey`) for Google Scholar requests.
- **NewsAPI API Key** (`newsApiKey`) for news articles.

Make sure to add these credentials securely in your system before executing the workflow.

---

## Customization & Improvements

- Replace **Placeholder NLP - Summarization** node with an integration to an NLP service or custom code to:
  - Generate summaries
  - Identify emerging trends
  - Score or rank papers/articles
- Adjust the number of results fetched from each source by modifying request URLs.
- Update subscriber list as needed or connect to dynamic subscriber management.
- Enhance email formatting (HTML emails, attachments, etc.).

---

## Summary

This workflow provides a comprehensive automated pipeline to track the latest quantum computing research trends by aggregating and notifying users about new academic publications and news. It offers a modular design allowing easy expansion with advanced natural language processing and alerting capabilities.