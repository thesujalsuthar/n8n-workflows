# Quantum Computing Research Trend Analyzer and Alert System

This workflow automates the process of gathering, analyzing, and distributing the latest trends in quantum computing research by aggregating data from research papers, news articles, and patents. It uses advanced AI models to perform sentiment and trend analysis and delivers summarized insights to subscribed users via email and Slack notifications.

---

## Workflow Overview

### 1. Data Collection

- **Fetch Arxiv Papers**  
  Retrieves the latest 10 quantum computing related research papers from the Arxiv API, sorted by submission date (most recent first).  
  **Endpoint:** `https://api.arxiv.org/query`  
  **Format:** XML (string)

- **Fetch News Articles**  
  Retrieves news articles mentioning "quantum computing" using the NewsAPI. Articles are sorted by publication date (most recent first).  
  **Endpoint:** `https://newsapi.org/v2/everything`  
  **Format:** JSON  
  **Credentials Required:** NewsApi API Key

- **Fetch Patents**  
  Queries the PatentsView API for recent patents related to "quantum computing", fetching patent numbers, titles, dates, and abstracts.  
  **Endpoint:** `https://api.patentsview.org/patents/query`  
  **Format:** JSON

---

### 2. Data Parsing and Normalization

- **Parse Arxiv Data**  
  Converts the XML response from Arxiv into structured JSON objects containing paper titles, summaries, publication dates, and authors.

- **Parse News Data**  
  Extracts and structures article titles, descriptions, publication dates, sources, and URLs from the NewsAPI JSON.

- **Parse Patents Data**  
  Structures patent metadata including patent number, title, date, and abstract from PatentsView JSON.

- **Combine and Normalize Data**  
  Merges the parsed data from papers, news, and patents into a unified format with a type identifier (`paper`, `news`, or `patent`) to facilitate further analysis.

---

### 3. AI-Powered Analysis

- **AI Sentiment and Trend Analysis**  
  Sends the combined data to an AI model (OpenAI GPT-4) to perform sentiment analysis, detect emerging trends, and generate a summarized insight report.  
  **Parameters:**  
  - Model: GPT-4  
  - Temperature: 0.3 (to maintain focused and relevant output)  
  - Max Tokens: 500  
  - Messages: System and user prompts designed for research data analysis.

- **Extract Summary**  
  Extracts the textual summary content from the AI response to be used in notifications.

---

### 4. Alert Distribution

- **Get Users with Channels**  
  Fetches a list of subscribed users along with their contact details (email and Slack channels).

- **Split Users for Alerts**  
  Splits the user list into batches to efficiently send alerts without overwhelming the system.

- **Send Email Alerts**  
  Sends the AI-generated summarized insights as email notifications to subscribed users.  
  **Sender:** alerts@quantumresearch.io  
  **Recipients:** User emails from subscription list  
  **Credentials Required:** SMTP server credentials

- **Slack Send Alerts**  
  Posts the same summarized insights as messages on users' designated Slack channels.  
  **Credentials Required:** Slack API token

---

## Credentials Required

- **NewsApi Credentials**: API key for NewsAPI to fetch news articles.
- **OpenAI API**: API key for accessing OpenAI's GPT-4 model.
- **SMTP Credentials**: SMTP server credentials for sending email notifications.
- **Slack API**: OAuth token for sending Slack messages.

---

## Configuration Details

- **From Email Address**: `alerts@quantumresearch.io` (used for sending email alerts)
- **Subscribed Users**: Hardcoded list in the workflow with their emails and Slack channel IDs; can be replaced by a dynamic subscriber management system.
- **Slack Channel Defaults**: If no specific Slack channel is provided for a user, a default channel `C1234567890` is used.

---

## Execution Flow Summary

1. **Fetch latest quantum computing data** from Arxiv, News, and Patents APIs.
2. **Parse and normalize** the data into a consistent JSON format.
3. **Combine** all data entries.
4. **Analyze** combined data with OpenAI for sentiment and trend insights.
5. **Extract** AI-generated summary.
6. **Retrieve subscribed users** with contact details.
7. **Batch the users** for scalable alert dispatch.
8. **Send alerts** via email and Slack.

---

## Notes

- The workflow is designed for periodic execution to keep subscribers updated on the latest trends.
- Batch processing ensures scalability when the subscriber list grows.
- AI analysis parameters can be adjusted to modify the depth or style of the analysis.
- XML parsing in JavaScript functions uses DOMParser, which requires a compatible environment.
- Modify the hardcoded user lists with real subscriber management mechanisms as needed.

---

## License

This workflow and associated code snippets are provided as-is for research and educational purposes. Adapt and extend according to your requirements.