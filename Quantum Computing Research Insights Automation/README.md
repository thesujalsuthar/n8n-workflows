# Quantum Computing Weekly Research Workflow

This n8n workflow automates the process of fetching, parsing, aggregating, storing, and notifying about the latest quantum computing research publications, preprints, and tech news. It runs daily and highlights significant breakthroughs or collaboration/funding opportunities.

---

## Workflow Overview

### 1. Start & Trigger
- **Start Node**: Entry point for the workflow.
- **Daily Trigger**: Executes the workflow every day at 09:00 AM.

### 2. Data Fetching Nodes
These nodes simultaneously retrieve the latest content about quantum computing from three different sources:

- **Fetch CrossRef Publications**  
  Uses the CrossRef API to get peer-reviewed publications from the last 7 days filtered by the subject "quantum computing."  
  - Request URL example:  
    `https://api.crossref.org/works?filter=subject:quantum+computing,from-pub-date:{{7_days_ago}}&sort=published,order=desc`
  - Response format: JSON.

- **Fetch arXiv Preprints**  
  Queries the arXiv API for recent preprints about quantum computing, limited to 10 results sorted by submission date descending.  
  - Request URL:  
    `https://api.arxiv.org/query?search_query=all:quantum+computing&start=0&max_results=10&sortBy=submittedDate&sortOrder=descending`
  - Response format: XML.

- **Fetch Tech News**  
  Calls NewsAPI to retrieve the latest tech news articles mentioning "quantum computing," sorted by publication date.  
  - Request URL (requires your API key):  
    `https://newsapi.org/v2/everything?q=quantum+computing&sortBy=publishedAt&apiKey=YOUR_NEWSAPI_KEY`  
  - Response format: JSON.

### 3. Parse and Combine Data
- This Function node processes and normalizes the responses:
  - Parses the JSON from CrossRef.
  - Parses XML from arXiv using `xml2js`.
  - Extracts relevant fields from the NewsAPI JSON.
- Combines all entries into a unified format with the following fields for each item:  
  `title`, `authors`, `date`, `url`, and `source` (CrossRef, arXiv, or Tech News).
- Outputs an array of standardized entries for the following steps.

### 4. Append to Google Sheets
- Stores all combined entries to a Google Sheet.
- Appends data to the range `Sheet1!A:E`.
- Fields written in order: Date, Title, Authors, URL, Source.
- Requires Google Sheets API credentials and your Sheet ID (`YOUR_GOOGLE_SHEET_ID`).

### 5. Send Summary Email
- Sends a daily summary email containing the latest quantum computing updates.
- From and To addresses should be set (`your@email.com` and `recipient@email.com`).
- The email body lists the title, source, and URL for each entry.
- Subject: "Weekly Quantum Computing Research Summary".

### 6. Check for Significant Breakthroughs
- This conditional (IF) node checks if any entry titles contain breakthrough-related keywords such as:  
  `"breakthrough"`, `"significant"`, `"milestone"`, `"important"`, `"collaboration call"`, `"funding opportunity"`.
- If at least one entry contains any keyword, the workflow triggers an alert email.

### 7. Send Alert Email
- Sends an urgent email alerting recipients about notable breakthroughs or collaboration/funding opportunities discovered.
- From and To addresses to be set (`your@email.com` and `recipient@email.com`).
- Subject: "Quantum Computing Significant Breakthroughs or Collaboration Calls Alert".
- Email body includes the title, source, and URL of the relevant entries.

---

## Setup and Configuration

### Required Credentials and API Keys
- **NewsAPI Key**: Replace `YOUR_NEWSAPI_KEY` in the Fetch Tech News node URL with your valid NewsAPI key.
- **Google Sheets API**: Set up credentials and replace `YOUR_GOOGLE_SHEET_ID` with the target Google Sheet ID.
- **Email Credentials**: Configure SMTP or email account credentials for the Send Summary Email and Send Alert Email nodes.

### Google Sheet Format
Create or designate a Google Sheet with at least the following columns in `Sheet1`:
| Date       | Title                       | Authors           | URL                | Source     |

---

## Node Connection Summary

- The workflow starts with the **Start** node connected to the **Daily Trigger**, which fires every morning at 9:00.
- On trigger, it performs parallel HTTP requests to:
  - CrossRef Publications
  - arXiv Preprints
  - Tech News
- All fetched data is sent to the **Parse and Combine Data** node.
- Parsed and combined data is then routed to three parallel branches:
  - Append entries to Google Sheets.
  - Send a summary email.
  - Perform the keyword check for significant breakthroughs.
- If breakthroughs are detected, an alert email is sent.

---

## Notes
- The workflow runs once per day but can be adjusted in the **Daily Trigger** node schedule parameters.
- The XML parsing depends on the `xml2js` library available within n8n's function nodes.
- Modify breakthrough keywords in **Check for Significant Breakthroughs** node to suit your monitoring needs.
- Ensure API rate limits are managed for all external services.

---

## License

This workflow is provided "as-is" without warranty. Modify and extend it according to your requirements.