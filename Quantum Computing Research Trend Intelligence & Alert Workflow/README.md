# Quantum Computing AI Research Trend Analyzer & Real-Time Alert System

## Overview

This workflow automates the process of monitoring, analyzing, and alerting on the latest trends in quantum computing research. It fetches recent academic publications and news articles, employs AI to analyze sentiment and key topics, aggregates trends, and sends real-time alerts via email and Slack to subscribed users.

---

## Workflow Components

### 1. Fetch Research Publications
- **Type:** HTTP Request  
- **API:** CrossRef REST API  
- **Purpose:** Retrieves the 10 most recent scholarly articles related to "quantum computing".  
- **Query Parameters:**  
  - `query=quantum+computing`  
  - Sorted by publication date descending  
  - `rows=10` results  
- **Output:** JSON list of publications with metadata including title, abstract, authors, and URLs.

### 2. Fetch News Articles
- **Type:** HTTP Request  
- **API:** NewsAPI  
- **Purpose:** Collects latest news articles related to "quantum computing".  
- **Query Parameters:**  
  - `q=quantum computing`  
  - `sortBy=publishedAt`  
  - `language=en`  
  - `apiKey` (provided via credentials)  
- **Output:** List of news articles including title, description, author, URL, and publishing date.

### 3. Merge Content
- **Type:** Function  
- **Purpose:** Normalizes and merges publications and news articles into a unified format with consistent fields:  
  - `source` (publication or news)  
  - `title`  
  - `abstract` or description  
  - `url`  
  - `publishedAt`  
  - `authors`  
  - `content` (combined textual content for analysis)  

### 4. AI Sentiment & Topic Analysis
- **Type:** OpenAI API (GPT-4o-mini)  
- **Purpose:** For each combined content item, performs:  
  - Sentiment analysis (positive, neutral, or negative)  
  - Topic extraction to identify main themes  
- **Prompt:** Provides title and content, asking for a JSON response containing sentiment and topics.

### 5. Parse AI Response
- **Type:** Function  
- **Purpose:** Parses the raw AI JSON responses and attaches sentiment and topic data back to the original content item.  
- **Fallback:** Defaults to neutral sentiment and empty topics if parsing fails.

### 6. Aggregate Trends
- **Type:** Function  
- **Purpose:** Aggregates analyzed items by topics and sentiment, organizing data into a structured summary:  
  - For each topic: lists positive, neutral, and negative items  
  - Includes title, source, URL, and publication date

### 7. Subscription List
- **Type:** Function (mock)  
- **Purpose:** Retrieves list of subscribers with their email addresses and Slack channels.  
- **Note:** Currently mocked with one subscriber:
  - Email: `researcher@example.com`  
  - Slack channel: `#quantum-research`

### 8. Send Email Alert
- **Type:** Email Send  
- **Purpose:** Sends a summary email to each subscriber containing:  
  - Topic-wise breakdown of trends by sentiment  
  - Up to 3 example items per sentiment category  
- **Email Details:**  
  - From: `quantum-alerts@researchhub.org`  
  - Subject: Real-Time Quantum Computing Research Trend Alert

### 9. Send Slack Alert
- **Type:** Slack message post  
- **Purpose:** Posts the same summary to the subscriber’s designated Slack channel for real-time collaboration and discussion.  
- **Content:** Formatted overview highlighting sentiments and topics with clickable links.

---

## Execution Flow

1. **Fetch Research Publications** →  
2. **Fetch News Articles** →  
3. **Merge Content** →  
4. **AI Sentiment & Topic Analysis** (parallel AI calls for each content item) →  
5. **Parse AI Response** →  
6. **Aggregate Trends** →  
7. **Subscription List** →  
8. **Send Email Alert** & **Send Slack Alert**

---

## Requirements & Configuration

- **APIs Needed:**
  - CrossRef API (public, no key required)  
  - NewsAPI (API key required)  
  - OpenAI API (with access to GPT-4o-mini or equivalent)  
  - Email service configured for sending alerts  
  - Slack workspace and bot configured for posting messages  

- **Credentials:**
  - `newsApi.apiKey` must be set in workflow credentials  
  - Email sending credentials configured in the workflow environment  
  - Slack credentials (bot token) configured for Slack node

- **Subscription Management:**
  - Currently mocked function; replace the **Subscription List** node with real retrieval logic (e.g., from database or spreadsheet).

---

## Customization

- Adjust query parameters in the **Fetch Research Publications** and **Fetch News Articles** nodes for different keywords or filtering criteria.
- Modify the AI prompt in **AI Sentiment & Topic Analysis** for deeper analysis or additional outputs.
- Expand the **Subscription List** to dynamically fetch subscribers.
- Enhance alert formatting or add new delivery channels as needed.

---

## Summary

This workflow enables continuous monitoring of quantum computing research landscape by integrating scientific publications and news media, leveraging AI for insightful trend analysis, and delivering timely alerts to researchers and stakeholders through email and Slack. It serves as a powerful tool to stay updated and informed on emerging quantum computing topics and sentiments.