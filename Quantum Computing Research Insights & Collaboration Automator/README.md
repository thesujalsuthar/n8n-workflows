# Quantum Computing Research Collaboration and Innovation Tracker

## Overview

This workflow automates the collection, analysis, and notification of the latest quantum computing research trends, conferences, and collaboration platforms. It fetches recent research papers, upcoming conferences, and active GitHub repositories related to quantum computing, analyzes keyword trends, and sends personalized email notifications and Slack summaries to keep the research community informed and engaged.

---

## Workflow Breakdown

### 1. Start

- The initial trigger node that starts the workflow execution.

### 2. Fetch Research Papers

- **Type:** HTTP Request  
- **Function:** Queries the CrossRef API to fetch the **20 most recent** quantum computing research papers.  
- **Endpoint:** `https://api.crossref.org/works`  
- **Parameters:**  
  - Query: `quantum computing`  
  - Rows: `20`  
  - Sorted by published date in descending order.

### 3. Fetch Conference Feeds

- **Type:** HTTP Request  
- **Function:** Retrieves details about upcoming quantum computing conferences.  
- **Endpoint:** `https://api.conferencealerts.com/api/v1/events`  
- **Parameters:**  
  - Query: `quantum computing`  
  - Limit: `20`  
- **Authentication:** Requires an API Key passed in the `Authorization` header as a Bearer token (`YOUR_CONFERENCE_API_KEY` placeholder).

### 4. Fetch Collaboration Platforms (GitHub)

- **Type:** HTTP Request  
- **Function:** Searches GitHub repositories relevant to quantum computing created since January 1, 2023, and sorts by stars in descending order.  
- **Endpoint:** `https://api.github.com/search/repositories`  
- **Query:** `quantum computing` in description, filtered by creation date from 2023-01-01 onward.  
- **Authentication:** GitHub Personal Access Token required in the header (`YOUR_GITHUB_TOKEN` placeholder).

### 5. Merge Data

- **Type:** Merge Node  
- **Function:** Combines fetched data from the three sources (`research papers`, `conference feeds`, and `GitHub repos`) into a single data structure keyed by `id`.  
- **Join Mode:** Append all data arrays into a property called `allData`.

### 6. Analyze Trends and Keywords

- **Type:** Function Node  
- **Function:**  
  - Parses and processes the merged data arrays.  
  - Extracts frequency counts for key quantum computing terms:  
    - `quantum computing`  
    - `qubit`  
    - `quantum algorithm`  
    - `quantum hardware`  
    - `quantum error correction`  
  - Searches titles, abstracts, descriptions, and names from papers, conferences, and repos.  
  - Outputs total counts and keyword frequencies.

### 7. Generate Summary

- **Type:** Function Node  
- **Function:**  
  - Constructs a textual summary of the latest data:  
    - Number of new research papers.  
    - Number of upcoming conferences.  
    - Number of new collaboration repositories.  
    - Frequency counts of key terms.  
  - Adds this summary as a new property `summary` in the workflow output.

### 8. Send Personalized Notifications

- **Type:** Email Send Node  
- **Function:** Sends an email summary to a specified recipient with:  
  - Total counts of papers, conferences, and repositories.  
  - Highlights of keyword frequency trends.  
- **Recipient:** Dynamic, uses `$json["userEmail"]` if available, otherwise defaults to `user@example.com`.  
- **Subject:** `Quantum Computing Research Update`.

### 9. Post Summary To Slack

- **Type:** Slack Node  
- **Function:** Posts the generated summary message to a designated Slack channel.  
- **Channel:** Set via `YOUR_SLACK_CHANNEL_ID` placeholder.  
- **Message:** The textual summary generated in the previous node.

---

## Setup Instructions

1. **Get API Credentials:**  
   - Register and obtain API keys or tokens for:  
     - Conference Alerts API (`YOUR_CONFERENCE_API_KEY`)  
     - GitHub Personal Access Token (`YOUR_GITHUB_TOKEN`)  
     - Slack Bot Token with necessary permissions and Slack Channel ID (`YOUR_SLACK_CHANNEL_ID`)  

2. **Configure Nodes:**  
   - Replace placeholder tokens in HTTP Request and Slack nodes with your actual API keys and tokens.  
   - Set the email recipient in the Email node as needed or ensure your input JSON provides `userEmail`.  

3. **Activate Workflow:**  
   - Ensure all nodes are correctly connected and configured.  
   - Activate the workflow to enable automation.

---

## Usage

- Once activated, the workflow will:  
  - Automatically fetch up-to-date quantum computing research content from multiple sources.  
  - Analyze current trends and keyword prominence.  
  - Deliver personalized email notifications with detailed counts and highlights.  
  - Post a comprehensive summary to a team Slack channel for broader collaboration awareness.

---

## Customization Tips

- **Adjust Query Parameters:**  
  - Modify API query parameters such as number of results, sorting, or keywords to tailor data scope.  

- **Expand Keywords:**  
  - Add or change the keywords in the Analyze Trends function to reflect evolving research interests.

- **Extend Notifications:**  
  - Add more channels, e.g., SMS, other chat platforms, or dashboards as needed.

---

## Workflow Visual Summary

```plaintext
Start
  ├─> Fetch Research Papers
  ├─> Fetch Conference Feeds
  ├─> Fetch Collaboration Platforms (GitHub)
           └─> Merge Data
                     ├─> Analyze Trends and Keywords ──> Send Personalized Notifications (Email)
                     └─> Generate Summary ──> Post Summary To Slack
```

---

## Notes

- This workflow leverages the n8n automation platform.  
- Proper error handling and rate-limit management should be considered for production use.  
- API usage may incur costs or limits depending on service plans.

---

Thank you for using the Quantum Computing Research Collaboration and Innovation Tracker!