# AI-Driven Quantum Research Collaboration and Insights Dashboard

This workflow automates the process of discovering recent quantum computing research papers, analyzing their content for trends and sentiments using AI, and notifying researchers with summarized insights and collaboration opportunities.

---

## Workflow Overview

1. **Webhook Trigger**  
   Initiates the workflow via an HTTP webhook request.

2. **Fetch Quantum Papers**  
   Sends a GET request to the Semantic Scholar API to retrieve the latest 10 papers related to "quantum computing". The fields fetched include title, abstract, authors, year, publication venue, URL, and citation count.

3. **Parse Papers Data**  
   Processes the raw API response, extracting and formatting key paper information such as title, abstract, author names, publication year, venue, and URL.

4. **Analyze Trends & Insights**  
   Uses an OpenAI GPT-4o-mini model to analyze each paper’s title and abstract, generating detailed trends and insights.

5. **Sentiment Analysis**  
   Performs sentiment analysis on each paper’s title and abstract to gauge the overall tone or attitude.

6. **Combine Insights & Sentiment**  
   Merges the AI-generated insights and sentiment results for each paper, preparing a structured summary.

7. **Send Notification Email**  
   Sends an email to the first author of each paper, including the title, AI-generated insights, sentiment analysis, and a URL link to the paper. The email encourages collaboration among researchers.

---

## Node Details

### 1. Webhook Trigger
- **Type:** Webhook  
- **Webhook Path:** `quantum-research-update`  
- **Purpose:** Starts the workflow upon receiving an HTTP request.

### 2. Fetch Quantum Papers
- **Type:** HTTP Request  
- **API Endpoint:** `https://api.semanticscholar.org/graph/v1/paper/search`  
- **Method:** GET  
- **Query Parameters:**  
  - `query`: "quantum computing"  
  - `limit`: 10  
  - `fields`: title, abstract, authors, year, venue, url, citations

### 3. Parse Papers Data
- **Type:** Function  
- **Task:** Extracts and formats relevant fields from the API JSON response to create clean, usable paper data items.

### 4. Analyze Trends & Insights
- **Type:** OpenAI  
- **Model:** GPT-4o-mini  
- **Input:** Concatenation of paper title and abstract for context  
- **Output:** AI-generated analysis on trends and insights relevant to the paper.

### 5. Sentiment Analysis
- **Type:** Sentiment Analysis  
- **Input:** Concatenation of paper title and abstract  
- **Language:** English  
- **Output:** Sentiment score or category indicating the paper's tone.

### 6. Combine Insights & Sentiment
- **Type:** Function  
- **Task:** Combines the insights and sentiment for each paper into a single JSON object including the title, URL, insights, and sentiment.

### 7. Send Notification Email
- **Type:** Email Send  
- **From:** `quantum-research@domain.com`  
- **To:** Automatically generated from the first author's name (e.g. firstauthor@example.com)  
- **Subject:** Quantum Computing Research Update and Collaboration Opportunity  
- **Body:** Includes paper title, AI insights, sentiment, and a link to the paper with a call for collaboration.

---

## How to Use

1. Deploy this workflow in your n8n instance.
2. Configure the OpenAI credentials and email sending setup as needed.
3. Trigger the webhook (e.g., via HTTP request) to start the process.
4. Researchers will receive periodic emails summarizing new quantum computing research papers, with AI-driven insights and sentiment analysis.

---

## Benefits

- **Automated Literature Monitoring:** Automatically gathers recent relevant research papers.
- **AI-Powered Insights:** Uses GPT-4o-mini to highlight trends and important findings.
- **Sentiment Context:** Provides tone detection to help understand research attitudes or focuses.
- **Collaboration Encouragement:** Directly notifies authors for networking and joint efforts.

---

## Requirements

- n8n automation platform
- Access to Semantic Scholar API (public, no auth required for search endpoint)
- OpenAI API key configured in n8n credentials for GPT-4o-mini usage
- Email sending credentials configured in n8n

---

## License

This workflow content is provided as-is for educational and research collaboration purposes.