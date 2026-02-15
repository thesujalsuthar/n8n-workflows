# Quantum Computing Research Trend Analyzer & Alert System

This workflow aggregates recent publications and news articles about quantum computing, analyzes emerging trends based on keyword frequency, and sends summary alerts to subscribed users via email and chat messages. It is designed using n8n to automate data fetching, processing, and notifications.

---

## Workflow Overview

The workflow consists of the following key components:

1. **Subscription Management**  
   - A webhook to receive subscription requests with subscriber email and chat ID.  
   - Stores and manages subscriber information in persistent workflow static data.

2. **Data Fetching**  
   - Fetches recent research publications from **Crossref API** (last 7 days, "quantum computing" query).  
   - Retrieves recent papers from **arXiv API**, filtered and sorted by last updated date.  
   - Retrieves recent news articles from **NewsAPI**, filtered by the last 7 days and keyword "quantum computing".

3. **Data Parsing and Combination**  
   - Parses JSON and XML responses from APIs.  
   - Combines titles, abstracts, summaries, and content from all sources into a unified text corpus.

4. **Text Embedding for NLP**  
   - Uses OpenAI's embedding model (`text-embedding-3-large`) to create vector embeddings for the combined textual data.

5. **Trend Analysis**  
   - Performs simple keyword frequency extraction for a pre-defined set of quantum computing related terms to detect emerging trends.

6. **Reporting**  
   - Formats the keyword frequency counts into a plain-text summary report for the subscribers.

7. **Notification Delivery**  
   - Sends the summary report to subscribers via email and Telegram chat messages.

---

## Detailed Node Descriptions

### 1. Subscription Webhook  
- **Node Type:** Webhook  
- **Purpose:** Receives HTTP POST subscription requests with the format:
  ```json
  {
    "subscriber_email": "user@example.com",
    "subscriber_chat_id": "123456789"
  }
  ```  
- **Operation:** Passes data to Subscription Storage function.

### 2. Store Subscription  
- **Node Type:** Function  
- **Purpose:** Saves subscriber data in global static data to avoid duplicates and track active subscribers.

### 3. Fetch Crossref Publications  
- **Node Type:** HTTP Request  
- **API:** https://api.crossref.org/works  
- **Query:** Searches for "quantum computing" publications published within the last 7 days, returning 20 results sorted by published date descending.  
- **Response Format:** JSON.

### 4. Fetch arXiv Publications  
- **Node Type:** HTTP Request  
- **API:** https://api.arxiv.org/query  
- **Query:** Searches all categories for "quantum computing" (with spaces replaced by '+'), returns 20 results sorted by last updated date descending.  
- **Response Format:** XML (text format).

### 5. Fetch News Articles  
- **Node Type:** HTTP Request  
- **API:** https://newsapi.org/v2/everything  
- **Query:** Fetches news published in the last 7 days, English language, about "quantum computing", limited to 20 articles.  
- **Headers:** Requires `Authorization` header with Bearer token `YOUR_NEWSAPI_KEY`.  
- **Response Format:** JSON.

### 6. Parse & Combine Publication Data  
- **Node Type:** Function  
- **Purpose:**  
  - Parses JSON from Crossref.  
  - Parses XML from arXiv into JSON using `xml2js`.  
  - Collects and combines publication items from both APIs into structured arrays.

### 7. Extract News Articles  
- **Node Type:** Function  
- **Purpose:** Extracts article list from NewsAPI JSON response.

### 8. Combine Text Data  
- **Node Type:** Function  
- **Purpose:**  
  - Merges texts from Crossref (title + abstract), arXiv (title + summary), and News (title + description + content).  
  - Produces a large combined text for embedding and analysis.

### 9. Embed Text for NLP  
- **Node Type:** OpenAI (Embedding)  
- **Purpose:** Converts combined text into an embedding vector using OpenAI's `text-embedding-3-large` model for further NLP tasks and analysis.

### 10. Analyze Trends by Keywords  
- **Node Type:** Function  
- **Purpose:**  
  - Counts occurrences of predefined quantum computing keywords in the combined text.  
  - Outputs a frequency dictionary representing emerging trends and focus areas.

### 11. Format Summary Report  
- **Node Type:** Function  
- **Purpose:**  
  - Creates a human-readable report summarizing keyword trends sorted by frequency for the subscribers.

### 12. Prepare Notifications  
- **Node Type:** Function  
- **Purpose:**  
  - Fetches all subscribers from static data.  
  - Prepares notification entries combining subscriber info and the summary report.

### 13. Send Email Alert  
- **Node Type:** Email Send  
- **Purpose:**  
  - Sends the formatted trend alert report to subscriber emails.

### 14. Send Chat Alert  
- **Node Type:** Telegram  
- **Purpose:**  
  - Sends the trend alert report to subscriber Telegram chat IDs.

---

## Usage Instructions

### Prerequisites  
- n8n installed and running.  
- API keys for:  
  - NewsAPI (replace `YOUR_NEWSAPI_KEY` in the "Fetch News Articles" node).  
  - OpenAI API key configured in n8n credentials for the OpenAI node.  
- SMTP email credentials configured in n8n for email sending.  
- Telegram bot token configured in n8n Telegram credentials.

### Subscribing for Alerts  
Send a POST request with subscriber details (email and optionally Telegram chat ID) to the `/subscriptions` webhook endpoint exposed by n8n.

**Example:**
```bash
curl -X POST https://your-n8n-instance/webhook/subscriptions \
  -H "Content-Type: application/json" \
  -d '{"subscriber_email":"user@example.com","subscriber_chat_id":"123456789"}'
```

### Running the Workflow  
Trigger the workflow manually or schedule periodic executions (e.g., daily) to:  
- Fetch the latest data,  
- Analyze trends, and  
- Notify all subscribers with the latest quantum computing research trends.

---

## Keywords Tracked

- quantum  
- qubit  
- entanglement  
- superposition  
- quantum algorithm  
- quantum supremacy  
- quantum annealing  
- quantum error correction  
- quantum machine learning  
- quantum cryptography  
- quantum hardware  
- quantum simulation  
- quantum circuit

---

## Notes

- XML parsing for arXiv uses the `xml2js` library available in n8n Function nodes.  
- Trend analysis is a simple frequency count; advanced NLP or topic modeling can be added as enhancements.  
- Store subscription data persistently or migrate to an external database if needed for scalability.

---

## License  

This workflow is provided as-is for educational and research automation purposes. Customize and extend as needed.