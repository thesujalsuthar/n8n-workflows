# QuantumAI Research Digest Aggregator

## Overview

The **QuantumAI Research Digest Aggregator** is an automated workflow designed to collect, filter, summarize, and deliver the latest research articles related to Quantum Artificial Intelligence (Quantum AI) and Quantum Computing. It fetches RSS feeds from leading scientific and popular sources, extracts relevant new content, generates a concise daily digest using OpenAI's GPT-4o-mini model, and emails the summary to a specified recipient.

---

## Workflow Breakdown

### 1. Fetch RSS Feeds
- **Node Type:** HTTP Request
- **Purpose:** Retrieves RSS feeds from multiple sources:
  - [arXiv Computer Science AI](https://arxiv.org/rss/cs.AI)
  - [arXiv Quantum Physics](https://arxiv.org/rss/quant-ph)
  - [Nature Quantum Computing](https://www.nature.com/subjects/quantum-computing.rss)
  - [Quanta Magazine](https://www.quantamagazine.org/feed/)
- **Output:** Raw RSS XML data.

### 2. Parse RSS XML
- **Node Type:** XML Node
- **Purpose:** Parses the fetched RSS XML documents to extract the array of individual feed `items` (articles).
- **Output:** Structured JSON objects representing each feed item.

### 3. Filter Relevant Content
- **Node Type:** IF
- **Purpose:** Filters articles based on relevant keywords in the concatenated text fields (`title`, `description`, and `content:encoded`):
  - Keywords: `quantum ai`, `quantum artificial intelligence`, `quantum computing`, `quantum machine learning`
- **Output:** Only items matching keywords proceed.

### 4. Get Metadata & Published Date
- **Node Type:** Set
- **Purpose:** Extracts and prepares metadata fields such as published date for further filtering.
- **Output:** Enhanced feed items with metadata.

### 5. Filter Recent Articles
- **Node Type:** Filter
- **Purpose:** Filters articles to include only those published within the last 24 hours.
- **Output:** Articles published after the specified cutoff (last 24 hours).

### 6. Combine Text for Summary
- **Node Type:** Function
- **Purpose:** Concatenates the titles and descriptions of all filtered recent articles into a single combined text block for summarization.
- **Output:** JSON containing the `combinedText` string.

### 7. Summarize Digest (OpenAI)
- **Node Type:** OpenAI (GPT-4o-mini)
- **Purpose:** Generates a concise daily digest summarizing the combined text input with emphasis on insights, trends, and new developments in Quantum AI.
- **Prompt:**
    ```
    Summarize the following content into a concise daily digest focused on new developments in Quantum Artificial Intelligence, highlighting key insights and trends:

    {{$json["combinedText"]}}

    Summary:
    ```
- **Output:** AI-generated digest summary text.

### 8. Send Email
- **Node Type:** Email Send
- **Purpose:** Sends the summarized daily digest via email.
- **Parameters:**
  - **To:** your.email@example.com (customize with your email address)
  - **Subject:** Daily QuantumAI Research Digest
  - **Body:** AI-generated summary from the previous step.

---

## How to Use

1. **Import Workflow:** Load this workflow into your n8n instance.
2. **Configure Email:** Update the **Send Email** node with your email address and SMTP credentials.
3. **OpenAI Credentials:** Ensure your OpenAI API credentials are configured in n8n.
4. **Activate Workflow:** Enable the workflow and schedule it to run daily as needed.
5. **Receive Digest:** Check your inbox every day for the curated Quantum AI research summary.

---

## Notes

- The workflow currently fetches articles only from the last 24 hours.
- Relevant content filtering is keyword-based and may be refined further.
- OpenAI model `gpt-4o-mini` is used for efficient summarization.
- Email delivery depends on correct SMTP configuration.

---

## Contribution

Feel free to extend or customize sources, keywords, time filters, or summary prompt to better suit your research monitoring needs.