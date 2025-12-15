# Quantum Computing Research Collaboration and Innovation Tracker

## Overview
This workflow automates the aggregation, processing, and distribution of the latest research publications, conference announcements, and collaborative innovations in the field of quantum computing. It collects data from multiple authoritative sources, organizes the information by publication year, and sends updates to both a centralized dashboard and an email distribution list for research teams.

---

## Workflow Nodes Description

### 1. Scheduler Daily
- **Type:** Scheduler
- **Purpose:** Triggers the entire workflow once every 24 hours (86400 seconds).
- **Position:** Start node initiating data fetch processes daily.

### 2. Arxiv Quantum Physics RSS
- **Type:** HTTP Request
- **Purpose:** Fetches the latest quantum physics research publications via the Arxiv RSS feed (`https://arxiv.org/rss/quant-ph`).
- **Output:** Raw RSS XML string data.

### 3. Crossref Quantum Computing Publications
- **Type:** HTTP Request
- **Purpose:** Queries the Crossref API (`https://api.crossref.org/works`) for the 20 most recent publications filtered by the subject "quantum computing".
- **Output:** JSON formatted list of publications.

### 4. Quantum Computing Conferences API
- **Type:** HTTP Request
- **Purpose:** Retrieves information about upcoming and ongoing conferences related to quantum computing from `https://conferenceapi.net/api/v1/conferences`.
- **Output:** JSON formatted conference data.

---

## Data Extraction and Parsing Nodes

### 5. Parse Arxiv RSS Feed
- **Type:** Function
- **Purpose:** Parses the RSS XML string from Arxiv into structured JSON objects with fields:
  - `title`
  - `link`
  - `pubDate`
  - `description`
- **Functionality:** Uses XML DOM parsing to convert the RSS feed into usable publication data.

### 6. Extract Crossref Publications
- **Type:** Function
- **Purpose:** Extracts and restructures fields from the Crossref JSON response for each publication:
  - `title` (first element)
  - `authors` (formatted full names)
  - `doi`
  - `published` date
  - `url`
- **Functionality:** Maps through the response array to create lightweight publication objects.

### 7. Extract Conference Data
- **Type:** Function
- **Purpose:** Passes through the conference JSON data as-is for further processing.

---

## Data Aggregation and Organization Nodes

### 8. Merge All Data
- **Type:** Merge
- **Purpose:** Combines the three streams of parsed data from Arxiv, Crossref, and conference APIs into a single merged dataset for further processing.
- **Mode:** PassThrough merge, preserving each dataset as a separate input stream.

### 9. Group Data by Year
- **Type:** Function
- **Purpose:** Groups all merged data entries by the publication or event year extracted from available date fields (`pubDate`, `published`, or `date`).
- **Output:** An array where each element contains:
  - `year`: the grouped year
  - `count`: number of entries for that year
  - `items`: list of all entries belonging to that year

---

## Data Distribution Nodes

### 10. Send to Dashboard
- **Type:** Miscellaneous (Custom Workflow Trigger)
- **Purpose:** Forwards the grouped and aggregated data into the connected **Quantum Computing Dashboard** workflow for visualization and further analysis.
- **Input:** Grouped JSON data per year.

### 11. Send Email Notification
- **Type:** Email Send
- **Purpose:** Sends a daily email update to `research-team@quantum.example.com` with the aggregated titles and years of research papers and events.
- **Subject:** "Daily Quantum Computing Research Update"
- **Content:** Enumerates the latest entries in a clean list format for quick team awareness.

---

## Connections Summary

- **Scheduler Daily** triggers the three main API fetching nodes in parallel:
  - Arxiv Quantum Physics RSS
  - Crossref Quantum Computing Publications
  - Quantum Computing Conferences API

- Each data fetch node flows into its dedicated extraction/parsing function.

- Parsed data from all three sources are merged into one stream.

- Merged data is grouped by year.

- Grouped data outputs are sent simultaneously to:
  - Dashboard workflow
  - Email notification to the research team

---

## How to Use This Workflow

1. **Set Email Recipient:** Update the recipient email address in the "Send Email Notification" node to your own research group's mailing list.
2. **Configure Dashboard:** Ensure the "Quantum Computing Dashboard" workflow exists to receive data from the "Send to Dashboard" node.
3. **Activate Scheduler:** Enable the "Scheduler Daily" node to run once per day.
4. **Run Workflow:** The workflow will aggregate fresh data daily and push updates automatically.
5. **Monitoring:** Check email inbox and dashboard to monitor latest quantum computing research and conference activities.

---

## Data Sources

- **Arxiv Quantum Physics RSS Feed:** https://arxiv.org/rss/quant-ph
- **Crossref API:** https://api.crossref.org/works with subject filter "quantum computing"
- **Conference API:** https://conferenceapi.net/api/v1/conferences?q=quantum computing

---

## Notes

- The workflow gracefully handles different data formats (RSS XML, JSON).
- Grouping by year assists in understanding temporal trends in research and collaborations.
- Email notifications serve as a daily briefing to keep all collaborators informed.

---

Thank you for using the Quantum Computing Research Collaboration and Innovation Tracker!