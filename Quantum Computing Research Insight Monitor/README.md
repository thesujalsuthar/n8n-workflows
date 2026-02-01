# Quantum Computing Research Collaboration and Innovation Sentinel

## Overview

This workflow is designed to monitor, analyze, and disseminate real-time updates related to quantum computing research, news, and social media activity. It aggregates data from multiple sources, performs sentiment and trend analyses, filters for significant insights, and sends email alerts to a research team.

---

## Workflow Components

### 1. Fetch News Articles
- **Node Type:** HTTP Request  
- **Description:** Retrieves the latest news articles related to quantum computing from NewsAPI.  
- **API Endpoint:** `https://newsapi.org/v2/everything?q=quantum%20computing&sortBy=publishedAt&apiKey={{$credentials.newsApi.apiKey}}`  
- **Parameters:**  
  - Query: "quantum computing"  
  - Sorted by published date (most recent first)  
  - Response format: JSON  
- **Credentials:** Requires a valid NewsAPI key.

### 2. Fetch Scientific Publications
- **Node Type:** HTTP Request  
- **Description:** Fetches the 20 most recent scientific publications in the field of quantum computing from the CrossRef API.  
- **API Endpoint:** `https://api.crossref.org/works?filter=subject:quantum%20computing&sort=published&order=desc&rows=20`  
- **Response format:** JSON

### 3. Fetch Tweets
- **Node Type:** Twitter  
- **Description:** Retrieves up to 50 recent tweets containing the keywords or hashtag related to quantum computing.  
- **API Endpoint:** Twitter API `tweets/search/recent`  
- **Query:** `quantum computing OR #QuantumComputing`  
- **Return:** Full tweet data  
- **Credentials:** Requires valid Twitter API credentials.

### 4. Merge Data
- **Node Type:** Merge  
- **Description:** Combines data from the three sources — news articles, scientific publications, and tweets — into a single dataset keyed by unique IDs.

### 5. Sentiment Analysis
- **Node Type:** Text Sentiment  
- **Description:** Analyzes the combined textual content from the merged dataset for overall sentiment in English.  
- **Input:** Concatenated text from titles, abstracts, and tweets.  
- **Output:** Sentiment score quantifying positive or negative sentiment.

### 6. Trend Analysis
- **Node Type:** Text Keywords  
- **Description:** Extracts key trending terms (keywords) from the combined text data to identify prominent topics related to quantum computing.  
- **Language:** English

### 7. Filter Important Updates
- **Node Type:** If (Conditional)  
- **Description:** Filters updates based on conditions:  
  - Sentiment score greater than or equal to 0.7 (high positivity) OR  
  - Presence of the keyword "breakthrough" in detected trends.  
- **Purpose:** To identify updates worthy of immediate attention.

### 8. Send Email Alert
- **Node Type:** Email Send  
- **Description:** Sends an email summary to the research team containing sentiment data, key trends, and latest relevant updates.  
- **Email Details:**  
  - From: `n8n@yourdomain.com`  
  - To: `research-team@yourdomain.com`  
  - Subject: "Real-Time Quantum Computing Research Update"  
  - Body:   
    - Sentiment Score  
    - Top Trends (keywords)  
    - Latest combined updates from all sources

---

## Data Flow

1. **Fetch News Articles**, **Fetch Scientific Publications**, and **Fetch Tweets** run in parallel retrieving data from their respective APIs.
2. Their outputs are merged into a single dataset by the **Merge Data** node.
3. The merged data is sent to two parallel analytical processes: **Sentiment Analysis** and **Trend Analysis**.
4. The results of these analyses feed into the **Filter Important Updates** node.
5. If the filter condition is met, the **Send Email Alert** node is triggered, sending out the summarized update.

---

## Prerequisites

- **NewsAPI Credential:** You must have an API key from [NewsAPI](https://newsapi.org/) configured in your n8n credentials.
- **Twitter API Credential:** You must have valid Twitter API credentials configured in n8n.
- **Email Setup:** Proper SMTP settings and email credentials configured for the email node to send alerts.
- **n8n Instance:** Workflow is designed for use in the n8n automation platform.

---

## Configuration Notes

- Replace default email addresses (`n8n@yourdomain.com` and `research-team@yourdomain.com`) with your actual sender and recipient emails.
- Ensure API keys and credentials are securely stored and referenced in the credentials section.
- Adjust the number of results or filtering keywords as per your monitoring needs.
- Monitor rate limits for Twitter and NewsAPI to avoid disruptions.

---

## Summary

This workflow automates the collection and analysis of quantum computing research and media updates, identifies insightful trends and sentiment, and notifies the research team promptly to foster collaboration and innovation.