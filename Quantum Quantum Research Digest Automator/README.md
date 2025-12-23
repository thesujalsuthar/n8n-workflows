# Quantum Computing Research Digest Aggregator

This workflow automatically fetches, processes, summarizes, and emails a daily digest of the latest quantum computing research papers, articles, and news from multiple reputable sources.

---

## Workflow Overview

The workflow is composed of a sequence of nodes that perform the following tasks:

1. **Trigger Daily**  
   Automatically triggers the workflow every day at 08:00 AM.

2. **Fetch RSS Feeds**  
   Retrieves the latest RSS feeds from multiple sources related to quantum computing:
   - arXiv Quantum Physics RSS: https://arxiv.org/rss/quant-ph
   - Nature Quantum Computing RSS: https://www.nature.com/subjects/quantum-computing.rss
   - Quanta Magazine RSS: https://www.quantamagazine.org/feed/
   - Phys.org Quantum Technology RSS: https://phys.org/rss-feed/quantum-technology/
   - ScienceDaily Quantum Computing RSS: https://www.sciencedaily.com/rss/computers_math/quantum_computing.xml

3. **Parse RSS XML**  
   Parses the XML data from the RSS feeds to extract individual articles with the following information:
   - Title
   - Link
   - Publication Date (converted to ISO format)
   - Description
   - Source URL

4. **Merge Unique Articles**  
   Consolidates articles from all feeds and removes duplicates based on the article link, ensuring each article is unique.

5. **Categorize Articles**  
   Classifies each article into one of the following categories by analyzing keywords in the title and description:
   - Research Papers (keywords: paper, arxiv, research, journal)
   - News (keywords: news, announcement, update)
   - Articles (keywords: article, opinion, blog)
   - Tutorials (keywords: tutorial, guide, how-to)
   - Other (default category if none match)

6. **Summarize Articles**  
   Uses OpenAI's GPT-4 API to generate concise bullet-point summaries of each article’s content (description or title) with the following details:
   - Model: GPT-4
   - Prompt: Summarize as a helpful assistant specializing in quantum computing research
   - Max tokens: 150
   - Temperature: 0.3 (for consistent and focused summaries)

7. **Send Email Digest**  
   Sends a formatted daily email containing:
   - Article title
   - Category
   - Article URL
   - GPT-4 generated summary

   Email configuration:
   - From: quantum.digest@yourdomain.com
   - To: Specified per-item recipient email or defaults to your.email@domain.com
   - Subject: Daily Quantum Computing Research Digest
   - SMTP credentials required for sending emails

---

## Node Details

### 1. Trigger Daily
- **Type:** Schedule Trigger
- **Run time:** Every day at 08:00 AM
- **Purpose:** Automatically start the workflow without manual intervention.

### 2. Fetch RSS Feeds
- **Type:** HTTP Request
- **Action:** Fetches RSS XML data from multiple quantum computing sources.
- **Notes:** Supports multiple URLs in a single request.

### 3. Parse RSS XML
- **Type:** Function
- **Code:** Custom JavaScript function using `xml2js` to parse and extract article data from the RSS XML.

### 4. Merge Unique Articles
- **Type:** Merge
- **Operation:** `mergeByKey`
- **Key:** `link`
- **Purpose:** Eliminates duplicate articles based on their unique URLs.

### 5. Categorize Articles
- **Type:** Function
- **Code:** Classifies articles by checking keywords in the title and description to assign one of the predefined categories.

### 6. Summarize Articles
- **Type:** Function
- **Code:** Uses OpenAI GPT-4 API for summarization.
- **Credentials:** Requires valid OpenAI API key configured in n8n credentials.
- **Output:** Adds a concise summary to each article’s JSON data.

### 7. Send Email Digest
- **Type:** Email Send
- **Configuration:**
  - SMTP server credentials required
  - From and To fields are specified dynamically
- **Content:** Email body contains a list of articles with title, category, link, and summary formatted for easy reading.

---

## Prerequisites

- **n8n installation** with capability to run JavaScript functions and make HTTP requests.
- **OpenAI API key** for access to the GPT-4 model.
- **SMTP server credentials** to send email digests.
- Dependencies installed in the environment for parsing XML (`xml2js`) and natural language processing (`natural`) if applicable.

---

## Setup Instructions

1. **Configure Credentials:**
   - Add OpenAI API credentials in n8n under “Credentials” → “OpenAI API”.
   - Add SMTP credentials for your email service provider.

2. **Update Email Addresses:**
   - Modify the `fromEmail` in the "Send Email Digest" node to your desired sender email.
   - Adjust the default recipient email address or map it dynamically as needed.

3. **Deploy the Workflow:**
   - Import or create the workflow in your n8n instance.
   - Activate the workflow to enable daily automatic execution.

---

## Usage

- Once active, the workflow will run every morning at 8:00 AM.
- You will receive an email digest summarizing the latest quantum computing research from multiple trusted sources.
- You can customize the sources, categories, and summary style by modifying the relevant nodes.

---

## Troubleshooting

- Ensure all credentials (OpenAI, SMTP) are valid and correctly linked.
- Check network connectivity for accessing RSS feeds and OpenAI API.
- Review n8n logs if the workflow fails at any step.
- Adjust rate limits and request quotas as per API provider restrictions.

---

## License

This workflow is provided as-is without warranty. Feel free to customize and adapt it to your needs.

---

*Quantum Computing Research Digest Aggregator* - Stay informed effortlessly with daily updates on the frontier of quantum research!