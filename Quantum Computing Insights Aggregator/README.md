# Quantum Computing Collaboration and Innovation Tracker

This workflow automates the collection, aggregation, summarization, and distribution of the latest information related to quantum computing from multiple trusted sources, including academic journals, conference announcements, and social media. It enables researchers, professionals, and enthusiasts to stay informed about recent developments and collaboration opportunities in the quantum computing field.

---

## Workflow Overview

### 1. Academic Journals RSS
- **Node Type:** RSS Feed Read  
- **Purpose:** Fetches the 10 latest articles related to quantum computing from Nature's subject RSS feed.  
- **Source URL:** `https://www.nature.com/subjects/quantum-computing/rss`

### 2. Conference Announcements API
- **Node Type:** HTTP Request  
- **Purpose:** Retrieves upcoming quantum computing-related events from Conference Alerts API in JSON format.  
- **API Endpoint:** `https://api.conferencealerts.com/v1/events?subject=quantum+computing`

### 3. Twitter Recent Tweets
- **Node Type:** HTTP Request  
- **Purpose:** Fetches the 10 most recent tweets containing the keywords "quantum computing" or "quantumcomputing" using Twitter API v2.  
- **API Endpoint:**  
  ```
  https://api.twitter.com/2/tweets/search/recent?query=quantum%20computing%20OR%20quantumcomputing&max_results=10&tweet.fields=created_at,author_id,text
  ```  
- **Authentication:** Bearer Token (replace `YOUR_TWITTER_BEARER_TOKEN` with your Twitter API token)

### 4. Aggregate and Normalize Data
- **Node Type:** Function  
- **Purpose:** Normalizes and aggregates data from the three upstream nodes into a consistent structure consisting of:
  - `title` (paper title, event title, or tweet snippet)
  - `authors` (authors, organizers, or Twitter author ID)
  - `summary` (abstract, event description, or full tweet text)
  - `link` (URL to the paper, event, or tweet)  
- **Details:**  
  - Handles different data structures from RSS feed, conference events API, and Twitter responses.
  - Compiles all entries into a unified list for further processing.

### 5. Summarize Content
- **Node Type:** OpenAI (GPT-3.5 Turbo)  
- **Purpose:** Produces concise, clear, and professional summaries of the aggregated content, highlighting key points.  
- **Prompt:** Instructs the model to summarize research papers, conference announcements, and tweet data briefly.

### 6. Send Email Alert
- **Node Type:** Email Send  
- **Purpose:** Sends an email to the designated recipient (`user@example.com`) with the summarized insights extracted by the OpenAI model.  
- **Email Content:**  
  - Subject: "Quantum Computing Updates - New Research & Opportunities"  
  - Body: Summary of recent research, conferences, and social media insights encouraging collaboration.

---

## Optional / Disabled Nodes

### Store Aggregated Data (Disabled)
- **Node Type:** Write Binary File  
- **Purpose:** Save the normalized aggregated data locally at `data/quantum_tracker` for archival or offline analysis.  
- **Status:** Disabled by default.

### Personalized Recommendations (Disabled)
- **Node Type:** Merge  
- **Purpose:** Intended to merge data by user ID to offer personalized content or recommendations.  
- **Status:** Disabled by default.

---

## How It Works - Data Flow

1. **Data Sources:** Three separate nodes independently fetch data from academic journals, conference announcements, and Twitter.
2. **Data Aggregation:** All incoming data feed into the "Aggregate and Normalize Data" function, which standardizes the entries.
3. **Summary Generation:** The normalized content is sent to the OpenAI node to generate a clear, concise summary.
4. **Distribution:** Summary is emailed to the recipient for timely updates.
5. **Optional Storage & Personalization:** The aggregated data can be stored or merged for personalization, though these features are disabled by default.

---

## Configuration Requirements

- **Twitter API Token:**  
  Replace `"YOUR_TWITTER_BEARER_TOKEN"` in the "Twitter Recent Tweets" node with a valid Twitter API Bearer Token.

- **Email Recipient:**  
  Update the recipient email address in the "Send Email Alert" node (`user@example.com`) to your target address.

- **OpenAI API Access:**  
  Ensure OpenAI API credentials are configured in n8n to enable the "Summarize Content" node.

- **Optional:**  
  Enable and configure file path and personalization options if you want to use those features.

---

## Summary

This workflow provides an automated, efficient channel to track and stay updated on the rapidly evolving quantum computing domain by combining scholarly research, event notifications, and social media insights into concise summarized reports delivered directly to your inbox.