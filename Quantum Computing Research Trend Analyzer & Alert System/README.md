# Quantum Computing Research Trend Analyzer and Alert System

## Overview

This workflow monitors the latest research publications and preprints related to **Quantum Computing** from multiple scientific sources, analyzes key topics and sentiment within them, identifies emerging trends, and sends alerts via email and messaging platforms to keep users updated.

---

## Workflow Nodes Detailed Description

### 1. Fetch CrossRef Publications
- **Type:** HTTP Request
- **API endpoint:** `https://api.crossref.org/works?filter=subject:Quantum%20Computing&rows=20&sort=published&order=desc`
- **Purpose:** Retrieves the 20 most recent research publications related to Quantum Computing from CrossRef.
- **Response format:** JSON

---

### 2. Fetch bioRxiv Preprints
- **Type:** HTTP Request
- **API endpoint:** `https://api.biorxiv.org/covid19/new`
- **Purpose:** Retrieves the latest preprints from bioRxiv filtered for quantum computing (note: the given URL is COVID-19 preprints, you may customize it to target quantum computing preprints if available).
- **Response format:** JSON

---

### 3. Fetch arXiv Preprints
- **Type:** HTTP Request
- **API endpoint:** `https://api.arxiv.org/query?search_query=all:quantum+computing&sortBy=submittedDate&sortOrder=descending&max_results=20`
- **Purpose:** Obtains the 20 latest arXiv preprints related to Quantum Computing.
- **Response format:** XML

---

### 4. Aggregate and Normalize Publications
- **Type:** Function
- **Purpose:** Combines data from CrossRef, bioRxiv, and arXiv APIs into a unified, normalized structure.
- **Functionality:**  
  - Parses entries from CrossRef JSON.
  - Parses bioRxiv JSON data and reformats as needed.
  - Parses arXiv XML data using `fast-xml-parser`.
  - Extracts fields: `source`, `title`, `abstract`, `published date`, `authors`.
  - Outputs an array of normalized publication objects.

---

### 5. Extract Topics and Sentiment
- **Type:** OpenAI (text-davinci-003)
- **Purpose:** Analyzes each publication abstract and extracts key topics along with sentiment classification.
- **Prompt:** For each publication's Title and Abstract, return JSON containing:
  - `topics`: Array of key topics/keywords.
  - `sentiment`: One of `"positive"`, `"neutral"`, or `"negative"`.

---

### 6. Parse Topics and Sentiment
- **Type:** Function
- **Purpose:** Parses the response from OpenAI API.
- **Functionality:**  
  - Extracts topics and sentiment.
  - Assigns default sentiment `"neutral"` if parsing fails.
  - Structures final publication data including analysis.

---

### 7. Analyze and Rank Trends
- **Type:** Function
- **Purpose:** Aggregates topics from all publications and ranks them based on frequency and recency.
- **Process:**  
  - Tracks count of mentions and latest publication date for each topic.
  - Sorts topics primarily by frequency descending, and then by latest date descending.
- **Output:** List of topics with fields:
  - `topic`
  - `frequency` (mention count)
  - `recency` (most recent publication date)

---

### 8. Prepare Alert Message
- **Type:** Function
- **Purpose:** Composes a summary alert message for top 5 trending topics.
- **Format:**  
  ```
  Latest emerging quantum computing research trends and breakthroughs:

  - Topic 1 (Mentions: X, Latest: YYYY-MM-DD)
  - Topic 2 (Mentions: X, Latest: YYYY-MM-DD)
  ...

  Stay updated with the latest in quantum computing!
  ```
- **Outputs:**  
  - `emailSubject`: Email subject line.
  - `emailBody`: Email/messaging text content.

---

### 9. Send Email Alert
- **Type:** Email Send
- **Purpose:** Sends the composed alert message to user(s) via email.
- **Configuration:**
  - From: `alerts@quantumtrends.com`
  - To: User email (default: `user@example.com` if not set dynamically)
  - Subject & Body: Derived from previous node output.

---

### 10. Send Messaging Platform Alert
- **Type:** Slack (or other messaging platform)
- **Purpose:** Posts alert message to a messaging channel.
- **Configuration:**
  - Channel: Default `#quantum-alerts` or dynamically set via input.
  - Message: Same message text as email body.
  - Authentication: Uses OAuth2 credentials.

---

## Data Flow Summary

1. Fetch data from three sources (CrossRef, bioRxiv, arXiv).
2. Normalize and aggregate these publications into a consistent format.
3. Send each abstract to OpenAI to extract key topics and sentiment.
4. Parse OpenAI output and combine it with publication meta-data.
5. Analyze all topics to identify trending research themes by frequency and recency.
6. Generate alert message summarizing the top trends.
7. Send alerts via email and messaging platform.

---

## Prerequisites

- n8n automation platform installed and configured.
- API credentials and access tokens for:
  - OpenAI API (e.g., `text-davinci-003` model access).
  - Slack (or other messaging platform) with OAuth2 authentication.
  - Email sending configured in n8n.
- Required npm packages (within n8n function nodes) such as:
  - `fast-xml-parser`
  - `dayjs`

---

## Customization Tips

- Modify API URLs or filters to refine the search scope or number of results.
- Adjust OpenAI prompt or model parameters (temperature, max tokens) for different analysis depth.
- Change email recipients or message channels for alert distribution.
- Enhance the sentiment categories or topic extraction logic to suit needs.
- Add persistence or storage nodes for archiving publications or trends.

---

## Summary

This workflow offers a comprehensive automated pipeline to monitor, analyze, and alert on emerging research trends in Quantum Computing by aggregating diverse data sources, extracting insights using AI, and notifying stakeholders in near real-time.