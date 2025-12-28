# Quantum Collaboration and Innovation Tracker

## Overview

This workflow automates the process of tracking recent developments, publications, and collaboration opportunities in the field of quantum computing. It fetches academic and preprint research articles, identifies potential collaborators on LinkedIn, consolidates and logs data, generates summary reports, and notifies the team via Slack and email.

---

## Workflow Nodes and Descriptions

### 1. Fetch Preprint Publications

- **Type:** HTTP Request  
- **API:** [arXiv API](https://arxiv.org/help/api/)  
- **Description:** Fetches the 10 most recent preprint publications on quantum computing.  
- **Parameters:**  
  - `search_query`: "all:quantum computing"  
  - `start`: 0  
  - `max_results`: 10  
  - `sortBy`: lastUpdatedDate  
  - `sortOrder`: descending  
- **Response Format:** XML  

---

### 2. Fetch Academic Publications

- **Type:** HTTP Request  
- **API:** [CrossRef API](https://api.crossref.org/)  
- **Description:** Retrieves the 10 most recently published academic articles related to quantum computing since 2023-01-01.  
- **Parameters:**  
  - Filter by query: "quantum computing"  
  - From published date: 2023-01-01  
  - Rows: 10  
  - Sort by published date descending  
- **Response Format:** JSON  

---

### 3. Fetch Collaboration Opportunities

- **Type:** HTTP Request  
- **API:** LinkedIn People Search API  
- **Description:** Searches for LinkedIn profiles with keywords related to quantum computing to find potential collaborators.  
- **Headers:** Authorization with Bearer token using LinkedIn API credentials.  
- **Parameters:**  
  - `q`: people  
  - `keywords`: quantum computing  
  - `count`: 10  
- **Response Format:** JSON  

---

### 4. Consolidate Research Publications

- **Type:** Function  
- **Description:** Combines and maps data fetched from arXiv and CrossRef into a unified publication format with fields: title, authors, published date, source, and link.  
- **Logic:**  
  - Extracts relevant fields from both APIs.  
  - Converts author lists into comma-separated strings.  
  - Marks source as "arXiv" or "CrossRef".  

---

### 5. Generate Summary Report

- **Type:** Function  
- **Description:** Creates a daily summary report object with a title containing the current date and an array of consolidated publication entries.  
- **Output Format:** JSON object with `title` and `entries`.  

---

### 6. Log Raw API Data

- **Type:** Google Sheets (Append)  
- **Description:** Logs raw JSON response data from the publications APIs to a specified Google Sheet for archival and future analysis.  
- **Spreadsheet Range:** Sheet1!A:E  
- **Value Input Mode:** RAW  
- **Credentials:** Google Sheets OAuth2  

---

### 7. Create Report Document

- **Type:** Google Docs (Create)  
- **Description:** Creates a Google Doc containing the formatted summary report of all recent quantum computing publications.  
- **Document Title:** Based on report date  
- **Content:** JSON-formatted list of entries in a plain text document  
- **Credentials:** Google Docs OAuth2  

---

### 8. Send Summary Email

- **Type:** Gmail (Send)  
- **Description:** Sends an email to the research team with the summary report attached as a text file.  
- **Recipient:** research-team@example.com  
- **Subject:** Quantum Computing Research Summary  
- **Body:** Notification of the new summary report availability  
- **Attachment:** Google Docs summary document converted to a text file  
- **Credentials:** Gmail OAuth2  

---

### 9. Match Collaboration Opportunities

- **Type:** Function  
- **Description:** Processes LinkedIn search results to filter and extract profiles with "quantum" mentioned in their headline, identifying potential collaborators.  
- **Fields Extracted:** Name, headline, LinkedIn profile URL  

---

### 10. Notify Collaboration Channel

- **Type:** Slack (Post Message)  
- **Description:** Posts messages to the Slack channel `#quantum-collaboration` to notify about new matched collaboration opportunities.  
- **Message:** Includes name, headline, and profile link of each potential collaborator.  
- **Credentials:** Slack API  

---

## Workflow Connections

- Both preprint (arXiv) and academic publication (CrossRef) fetch nodes feed into the `Consolidate Research Publications` function.  
- The consolidated publications are sent to:  
  - `Generate Summary Report`  
  - `Log Raw API Data` (Google Sheets)  
- The summary report node feeds into `Create Report Document` (Google Docs).  
- The created document triggers the sending of the summary email via Gmail.  
- The LinkedIn fetch node connects to `Match Collaboration Opportunities`, which then triggers posting to the Slack collaboration channel.  

---

## Prerequisites and Setup

- **LinkedIn API Credentials:** Required for searching LinkedIn profiles. Must be added to n8n credentials as `LinkedIn API`.  
- **Google Sheets OAuth2 Credentials:** For appending raw data and must be set up in n8n.  
- **Google Docs OAuth2 Credentials:** For creating summary reports.  
- **Gmail OAuth2 Credentials:** For sending emails.  
- **Slack API Credentials:** For posting collaboration notifications to Slack.  
- **Google Sheet ID:** Replace `"your-google-sheet-id"` with the actual spreadsheet ID in the Log Raw API Data node.  

---

## Execution Flow Summary

1. Fetch latest quantum computing preprints and academic publications.  
2. Fetch LinkedIn profiles related to quantum computing.  
3. Consolidate publication data into a standardized format.  
4. Generate a summary report and log raw API responses.  
5. Create a Google Docs report document from the summary.  
6. Email the research team with the latest report attached.  
7. Match LinkedIn users to quantum collaboration opportunities.  
8. Notify the team in Slack with details of potential collaborators.  

---

## Notes

- The workflow is designed to run on-demand or on a schedule to keep the team informed of the latest research and collaboration possibilities in quantum computing.  
- All sensitive credentials and API keys should be managed securely within n8n.  
- Modify Google Sheet IDs, email addresses, and Slack channel names according to your organization's environment.