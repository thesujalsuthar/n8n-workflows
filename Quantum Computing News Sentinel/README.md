# Quantum Computing Trend Insight Aggregator and Alert System

## Overview
This workflow aggregates the latest news and insights on quantum computing, filters relevant articles based on user-defined keywords, performs sentiment analysis to detect critical developments, and sends alerts via email and Slack. It is designed to keep stakeholders informed with timely updates and urgent notifications on quantum computing trends.

---

## Components

### 1. Daily Scheduler
- **Type:** Schedule Trigger
- **Function:** Runs the workflow every day at 08:00 AM.
- **Purpose:** Automates daily execution to fetch and process news articles.

### 2. User Input
- **Type:** Function
- **Function:** Defines user preferences including:
  - Keywords to filter news articles related to quantum computing.
  - Email address for receiving alerts.
- **Default Keywords:**  
  - quantum  
  - qubit  
  - quantum algorithm  
  - superposition  
  - entanglement  
  - quantum error correction  
- **Default Email:** `recipient@example.com`  
- **Customization:** Modify this node to update keywords or recipient email.

### 3. Fetch News Articles
- **Type:** HTTP Request
- **Function:** Retrieves recent news articles related to quantum computing using the NewsAPI.
- **API Endpoint:** `https://newsapi.org/v2/everything?q=quantum+computing&sortBy=publishedAt&apiKey=YOUR_NEWSAPI_KEY`
- **Note:** Replace `YOUR_NEWSAPI_KEY` with your actual NewsAPI key.

### 4. Filter News by Keywords
- **Type:** Function
- **Function:** Filters the fetched news articles by checking if titles or descriptions include any of the user-defined keywords.
- **Output:** Only articles matching the keywords proceed for further processing.

### 5. Sentiment Analysis
- **Type:** Text Sentiment Analysis
- **Function:** Analyzes the sentiment of each article’s description (or title if description unavailable).
- **Purpose:** Helps assess the tone (positive, neutral, negative) of each news item.

### 6. Determine Critical Alerts
- **Type:** Function
- **Logic:**  
  - Marks an alert as *critical* if either:
    - Sentiment score is strongly negative (less than -0.5).
    - The article contains high-impact keywords like:  
      `breakthrough`, `critical`, `crash`, `failure`, `urgent`, `alert`.
- **Outcome:** Each article is flagged with an `isCritical` boolean.

### 7. Filter Critical Alerts
- **Type:** Function
- **Function:** Filters articles to include only those marked as critical alerts.
- **Purpose:** Ensures alerts are sent only for high-priority news.

### 8. Split Items for Notifications
- **Type:** Split In Batches
- **Function:** Processes notifications individually to manage sending multiple alerts.
- **Batch Size:** 1 per execution step.

### 9. Send Email Notification
- **Type:** Email Send
- **Function:** Sends an email alert for each critical news item.
- **From:** `alerts@yourdomain.com` (customize as necessary)
- **To:** User’s email address specified in the User Input node.
- **Subject:** Includes article title to highlight the critical update.
- **Body:** Contains detailed information about the alert:
  - Title  
  - Description  
  - Source name  
  - Publication date  
  - Link to full article  

### 10. Send Slack Notification
- **Type:** Slack Message
- **Function:** Sends a Slack message to the `quantum-alerts` channel for each critical alert.
- **Content:** Provides title, description, source, publication date, and a link.
- **Credentials:** Requires Slack API token configured in credentials (`Slack API`).

---

## Workflow Execution Flow

1. **Scheduled trigger** activates at 08:00 AM daily.
2. **User Input node** prepares the list of keywords and alert recipient email.
3. **Fetch News Articles** collects latest quantum computing news from NewsAPI.
4. **Filter News by Keywords** filters articles relevant to user interests.
5. **Sentiment Analysis** evaluates the tone of each selected article.
6. **Determine Critical Alerts** flags articles with negative sentiment or containing critical keywords.
7. **Filter Critical Alerts** narrows the list to high-priority alerts only.
8. **Split Items for Notifications** processes each alert separately.
9. Simultaneously sends:
   - **Email notifications** to the user's inbox.
   - **Slack notifications** to the designated Slack channel.

---

## Setup Instructions

1. **NewsAPI Key**
   - Obtain a free API key from [NewsAPI](https://newsapi.org/).
   - Replace the placeholder `YOUR_NEWSAPI_KEY` in the "Fetch News Articles" node.

2. **Email Configuration**
   - Configure SMTP credentials or email service in n8n.
   - Update the `fromEmail` address in "Send Email Notification" node.
   - Update recipient email in the "User Input" node or dynamically via an input form.

3. **Slack Configuration**
   - Set up a Slack app with permissions to post in the `quantum-alerts` channel.
   - Add the Slack credentials in n8n.
   - Update the Slack channel name in the "Send Slack Notification" node as needed.

4. **Customize Keywords and Recipients**
   - Modify the "User Input" node with your preferred keywords and email addresses.

5. **Activate Workflow**
   - Enable the workflow in n8n to start daily execution.

---

## Notes
- Ensure all API keys and credentials are securely stored in n8n credentials.
- Modify notification formats or channels as needed to suit your communication preferences.
- Increase batch size or add additional notification channels to scale the alert system.

---

## Summary
This system automates monitoring quantum computing news, extracts relevant insights based on user interests, evaluates the urgency via sentiment and keyword analysis, and promptly dispatches alerts to ensure you never miss critical updates.