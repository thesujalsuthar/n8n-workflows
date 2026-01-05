# Daily Quantum Computing Research Update Workflow

This workflow is designed to automatically collect, analyze, and distribute the latest research updates, news, and blog posts related to **quantum computing** on a daily basis.

---

## Overview

The workflow executes every day at 7:00 AM and performs the following key tasks:

1. **Data Collection**  
   Fetches the latest quantum computing content from three sources:
   - arXiv research papers search results
   - Google News RSS feed for quantum computing
   - Quantum Country blog RSS feed

2. **Data Parsing**  
   Extracts relevant information (e.g., titles, authors, abstracts, links, publication dates) from the raw HTML or XML responses.

3. **Data Combination**  
   Merges the parsed data from all three sources into a consolidated list.

4. **AI Analysis**  
   Uses OpenAI's GPT-4 model to analyze the combined data to:
   - Assign topic-related tags (e.g., Qubits, Quantum Algorithms, Quantum Hardware)
   - Determine sentiment (positive, neutral, negative)
   - Score the relevance of each item to quantum computing research (0 to 1 scale)

5. **Filtering Important Items**  
   Filters items with a relevance score above 0.7 and sorts them to keep the top 20 most relevant updates.

6. **Email Generation & Sending**  
   Builds a summary email listing the important updates, including titles, sentiment, tags, and links, and sends it to a predefined mailing list.

---

## Nodes Description

### 1. **Daily Scheduler**
- **Type:** Schedule Trigger
- **Function:** Triggers the workflow every day at 7:00 AM.

### 2. **Arxiv Research Scraper**
- **Type:** HTTP Request
- **Function:** Fetches search results from arXiv for the query "quantum computing."
- **Output:** Raw HTML string of search results.

### 3. **Google News RSS**
- **Type:** HTTP Request
- **Function:** Retrieves the RSS feed for Google News on the topic of "quantum computing."
- **Output:** RSS XML content as a string.

### 4. **Quantum Country Blog RSS**
- **Type:** HTTP Request
- **Function:** Retrieves the RSS feed from the Quantum Country blog.
- **Output:** RSS XML content as a string.

### 5. **Parse Arxiv Results**
- **Type:** Function
- **Function:** Parses the arXiv HTML to extract:
  - Title of each paper
  - Authors
  - Abstract
  - Link to the paper
- **Tools:** Uses `xpath` and `xmldom` libraries for HTML parsing.

### 6. **Parse Google News RSS**
- **Type:** Function
- **Function:** Parses the XML of Google News RSS to extract:
  - Title
  - Link
  - Publication date
  - Description

### 7. **Parse Quantum Country RSS**
- **Type:** Function
- **Function:** Parses the Quantum Country RSS feed extracting the same fields as the Google News RSS node.

### 8. **Combine Data**
- **Type:** Set
- **Function:** Combines the parsed results from the three sources into a single array stored in the `combined` property.

### 9. **AI Sentiment and Relevance Analysis**
- **Type:** OpenAI (GPT-4)
- **Function:** Sends the combined data list to GPT-4 with a prompt instructing it to analyze:
  - Sentiment (positive, neutral, negative)
  - Tags related to quantum computing topics (e.g., Qubits, Quantum Algorithms, Hardware, Error Correction, Cryptography)
  - Relevance score between 0 and 1
- **Output:** JSON array where each entry contains title, tags, sentiment, relevance, and link.

### 10. **Parse AI Response**
- **Type:** Function
- **Function:** Parses the AI-generated JSON string from GPT-4 into a usable JSON object.

### 11. **Filter Important Updates**
- **Type:** Function
- **Function:** Filters the AI analysis results to include only those with relevance > 0.7, sorts them by relevance descending, and returns the top 20.

### 12. **Build Email Content**
- **Type:** Function
- **Function:** Prepares a formatted text summary of the important updates for the email body, including:
  - Title
  - Sentiment
  - Tags
  - Link

### 13. **Send Email**
- **Type:** Email Send
- **Function:** Sends the daily summary email.
- **From:** `quantumalerts@example.com`
- **To:** `researchers@example.com`
- **Subject:** `Daily Quantum Computing Research Update`
- **Body:** Contains the formatted list of important updates (text and HTML versions).

---

## Usage Instructions

1. **Setup Required Credentials:**
   - Ensure you have configured the SMTP email credentials inside n8n to allow sending emails.
   - Configure the OpenAI API key in n8n for the GPT-4 node.

2. **Triggering:**
   - The workflow is set to run automatically every day at 7:00 AM.
   - You can manually trigger to test or adjust the scheduler as needed.

3. **Customization:**
   - Update email addresses (`fromEmail` and `toEmail`) in the "Send Email" node.
   - Adjust search queries or RSS feed URLs in the respective HTTP Request nodes to refine content sources.
   - Modify relevance threshold or number of items in the "Filter Important Updates" node if desired.

---

## Dependencies & Libraries

- **xpath** and **xmldom:** Used for parsing HTML from arXiv search results.
- **xml2js:** Used to parse RSS XML feeds (Google News and Quantum Country).
- **OpenAI GPT-4:** Used for content analysis including tagging, sentiment, and relevance evaluation.

---

## Summary

This workflow streamlines monitoring of the fast-evolving quantum computing research landscape by automating:
- **Data gathering** from scientific papers, news, and blogs
- **Intelligent analysis** to surface relevant, insightful updates
- **Daily email delivery** to keep researchers informed and up-to-date

It is an efficient tool for research teams, academic professionals, or enthusiasts who want a curated daily digest of the latest quantum computing developments.