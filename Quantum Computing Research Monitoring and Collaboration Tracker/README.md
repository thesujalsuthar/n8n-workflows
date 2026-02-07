# Quantum Computing Research Collaboration and Innovation Tracker

## Overview

This n8n workflow automates the tracking, analysis, and notification process for new quantum computing research papers and collaboration activities. It continuously monitors recent publications from ArXiv's Quantum Physics RSS feed, manages collaborative emails, analyzes research topic trends, and maintains shared Google Sheets for easy team access.

---

## Workflow Components

### 1. Fetch ArXiv Quantum Physics RSS Feed

- **Node:** `Fetch ArXiv Quantum Physics RSS Feed`  
- **Function:** Retrieves the latest quantum physics research papers via the RSS feed from [arXiv.org](https://arxiv.org/rss/quant-ph).

### 2. Parse RSS XML

- **Node:** `Parse RSS XML`  
- **Function:** Parses the retrieved RSS XML content into JSON to facilitate processing of paper details.

### 3. Extract Latest Paper Details

- **Node:** `Extract Latest Paper Details`  
- **Function:** Extracts key details from the latest paper:
  - Title
  - Link
  - Summary/Description
  - Publication Date
  - Authors (or 'Unknown' if unavailable)

### 4. Load Known Papers

- **Node:** `Load Known Papers`  
- **Function:** Loads existing recorded papers from a Google Sheets document using OAuth2 credentials to prevent duplicate alerts and entries.

### 5. Check If Paper Already Recorded

- **Node:** `Check If Paper Already Recorded`  
- **Function:** Compares the extracted latest paper's title and link against stored entries to determine if it is a new publication.

### 6. Filter Only New Papers

- **Node:** `Filter Only New Papers`  
- **Function:** Allows the flow to proceed only if the paper is not already known, effectively filtering out duplicates.

### 7. Send New Paper Alert

- **Node:** `Send New Paper Alert`  
- **Function:** Sends an email alert to the research team with details about the new paper, including title, authors, publication date, summary, and a direct link. Uses SMTP credentials.

### 8. Store New Paper in Google Sheets

- **Node:** `Store New Paper in Google Sheets`  
- **Function:** Appends the details of the new paper to the Google Sheets database for record-keeping.

### 9. Fetch Collaboration Emails

- **Node:** `Fetch Collaboration Emails`  
- **Function:** Retrieves emails labeled "collaborations" from the team Gmail account using OAuth2 credentials to monitor collaboration communications.

### 10. Extract Collaboration Email Data

- **Node:** `Extract Collaboration Email Data`  
- **Function:** Extracts relevant email details such as subject, sender, date, and snippet for streamlined reporting.

### 11. Store Collaboration Emails in Sheet

- **Node:** `Store Collaboration Emails in Sheet`  
- **Function:** Appends collaboration email data into a designated Google Sheet to maintain a log of team interactions.

### 12. Send Team Update Notification

- **Node:** `Send Team Update Notification`  
- **Function:** Sends a notification email to the team summarizing recent papers and collaboration updates to ensure alignment and awareness.

### 13. Analyze Research Topic Trends

- **Node:** `Analyze Research Topic Trends`  
- **Function:** Performs keyword frequency analysis on titles of known papers to identify trending research topics in quantum computing such as "quantum", "algorithm", "qubit", etc.

### 14. Update Trend Analysis Sheet

- **Node:** `Update Trend Analysis Sheet`  
- **Function:** Updates a dedicated Google Sheet with the latest keyword counts, facilitating continuous monitoring of research focus areas.

### 15. Store New Collaborative Project

- **Node:** `Store New Collaborative Project`  
- **Function:** Appends new project entries with metadata such as timestamp, project name, lead, status, and notes into the projects Google Sheet for team tracking.

---

## Credentials Required

- **Google Sheets OAuth2**  
  For reading from and writing to the Google Sheets databases, including papers, collaborations, trends, and projects.

- **SMTP Email**  
  For sending new paper alerts.

- **Gmail OAuth2**  
  For fetching collaboration emails and sending team update notifications.

---

## Configuration Notes

- Replace `"YOUR_COLLABORATION_SHEET_ID"`, `"YOUR_TRENDS_SHEET_ID"`, and `"YOUR_PROJECTS_SHEET_ID"` with the actual Google Sheets IDs corresponding to your collaborative data, trends analysis, and projects tracking sheets.

- Configure SMTP and Gmail OAuth2 credentials in n8n to allow secure, authenticated email communications.

---

## Execution Flow Summary

1. **Paper Monitoring:** The workflow fetches the latest quantum physics papers and extracts details.
2. **Duplicate Check:** Verifies if the paper is already recorded.
3. **Notifications & Storage:** If new, it alerts the team via email and stores the entry.
4. **Collaborations:** Concurrently retrieves collaboration emails, extracts data, and logs them.
5. **Trend Analysis:** Performs keyword trend analysis on accumulated paper titles.
6. **Documentation:** Updates sheets on papers, collaborations, trends, and projects.
7. **Team Update:** Sends summary notifications to keep the team informed.

---

## Benefits

- Automated monitoring of quantum computing research.
- Centralized tracking of new papers and collaborative communications.
- Trend analysis to identify evolving research directions.
- Streamlined team notifications for improved alignment and innovation.

---

## License

This workflow is provided as-is without warranty. Modify and use according to your collaboration needs.