# Quantum Computing Trend Monitoring Workflow

This workflow is designed to monitor the latest trends and developments in the field of quantum computing by aggregating data from multiple sources, analyzing sentiment and relevance, detecting emerging topics, and sending alerts via email and Slack. It runs automatically every 30 minutes.

---

## Workflow Overview

### 1. Trigger Every 30 minutes
- Initiates the workflow execution every 30 minutes to ensure up-to-date monitoring.

### 2. Fetch Publications - CrossRef
- Sends an HTTP GET request to the CrossRef API.
- Queries for the latest 20 publications related to "quantum computing".
- Results are sorted by publication date in descending order.
- Retrieves publication metadata including titles, abstracts, dates, and URLs.

### 3. Fetch News - NewsAPI
- Sends an HTTP GET request to the NewsAPI.
- Searches for the latest 20 news articles mentioning "quantum computing".
- Requires an API key stored securely as `newsApi` credentials.
- Returns article titles, descriptions, publication dates, and URLs.

### 4. Fetch Tweets - Twitter API v2
- Sends an HTTP GET request to Twitter’s recent search endpoint.
- Fetches up to 20 recent tweets related to "quantum computing".
- Uses Bearer Token stored under `twitterApi` credentials.
- Retrieves tweet text, creation time, and tweet IDs.

### 5. Combine & Flatten Data
- Custom JavaScript function node combines results from all three sources.
- Normalizes data into a unified list containing:
  - For publications: source, title, abstract, published date, URL.
  - For news: source, title, description, published date, URL.
  - For tweets: source, text, creation date, tweet ID.

### 6. Analyze Sentiment & Relevance - OpenAI
- Sends each combined item’s text content (title, abstract, description, or tweet text) to OpenAI GPT-3.5-Turbo.
- Prompts GPT to analyze sentiment (positive, negative, neutral), assign a relevance score (0 to 1), and provide a brief summary.
- Uses OpenAI credentials integrated into n8n.

### 7. Filter Relevant Trends
- JavaScript function filters out items with a relevance score below 0.6.
- Ensures only highly relevant quantum computing content proceeds.

### 8. Detect Emerging Topics
- Analyzes filtered content to detect the frequency of predefined keywords/topics related to quantum computing such as:
  - quantum advantage
  - quantum supremacy
  - qiskit
  - ion trap
  - superconducting qubit
  - quantum error correction
  - variational algorithm
  - quantum cryptography
  - quantum machine learning
- Counts occurrences to identify trending subtopics.

### 9. Prepare Summary Report
- Sorts detected topics by count descendingly.
- Selects top 5 emerging topics.
- Creates a summary string listing topics alongside their counts.

### 10. Send Alert Email
- Sends an email alert containing the summary report.
- Sender email: `alerts@quantumtrends.com`
- Recipient email is dynamically pulled from user credentials (`userEmail`).
- Email includes both plain text and HTML formatted content.

### 11. Send Slack Notification
- Sends a message to a specified Slack channel with the summary report.
- Uses Slack credentials (`slack.channelId` and access token) for authentication and channel targeting.
- Provides real-time notifications for team members.

---

## Credentials Required

- **NewsAPI API Key** (`newsApi`): For accessing latest news data.
- **Twitter API Bearer Token** (`twitterApi`): For retrieving recent tweets.
- **OpenAI API Key** (`openAi`): For sentiment and relevance analysis.
- **User Email** (`userEmail`): For sending email alerts.
- **Slack Token and Channel ID** (`slack`): For sending Slack notifications.

---

## How to Use

1. **Configure Credentials:**
   - Add your API keys and tokens in the n8n credential manager for NewsAPI, Twitter API, OpenAI, Email, and Slack.

2. **Activate Workflow:**
   - Enable the workflow so it triggers every 30 minutes automatically.

3. **Receive Alerts:**
   - Alerts are sent via email and posted in Slack with a summary of top emerging quantum computing topics.

---

## Customization

- **Query Adjustments:**
  - Modify search terms in the HTTP request URLs for publications, news, and tweets to track different topics.

- **Thresholds:**
  - Adjust relevance score threshold in the "Filter Relevant Trends" function node (default 0.6).

- **Keywords for Topic Detection:**
  - Update the keyword list in the "Detect Emerging Topics" node for more precise trend identification.

- **Alert Recipients:**
  - Change the sender and recipient email addresses or Slack channels as needed.

---

## Dependencies

- n8n version supporting HTTP Request, Function, OpenAI, Email, Slack, and Interval Trigger nodes.
- Valid API subscriptions for CrossRef (public), NewsAPI, Twitter API v2, and OpenAI GPT.

---

## Summary

This workflow automates continuous monitoring of quantum computing trends by aggregating multi-source data, leveraging AI to analyze sentiment and relevance, detecting emerging topics, and delivering timely alerts via email and Slack. It supports proactive trend tracking for researchers, analysts, and tech enthusiasts in the scientific community.