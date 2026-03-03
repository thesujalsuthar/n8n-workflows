# Quantum Computing Research Collaboration and Innovation Workflow

This workflow automates the process of gathering, analyzing, and notifying about recent research developments and collaboration opportunities in quantum computing. It leverages various APIs and AI tools to deliver insightful information to relevant stakeholders through Slack channels.

---

## Workflow Overview

The workflow consists of the following main steps:

1. **Fetch Recent Quantum Computing Papers**  
2. **Extract Paper Details**  
3. **Analyze Impact & Relevance Using AI**  
4. **Format AI Analysis Output**  
5. **Identify Collaboration Opportunities**  
6. **Notify Collaborators on Slack**  
7. **Generate Innovation Insights Report**  
8. **Send Report Notification to Community Slack Channel**

---

## Detailed Node Description

### 1. Fetch Recent Quantum Computing Papers (`HTTP Request`)

- **Purpose:** Connects to the CrossRef API to retrieve the 20 most recent papers related to "quantum computing" published since January 1, 2023.
- **API Used:** `https://api.crossref.org/works`
- **Query Parameters:**  
  - `query=quantum+computing`  
  - `filter=from-pub-date:2023-01-01`  
  - `sort=published`  
  - `order=desc`  
  - `rows=20`  
- **Output:** Raw JSON data containing metadata of the papers.

### 2. Extract Paper Details (`Function`)

- **Purpose:** Parses the API response to extract key details of each paper, including:
  - Title  
  - Authors (formatted as "GivenName FamilyName")  
  - Published date (formatted as YYYY-MM-DD)  
  - DOI  
  - URL  
  - Abstract (if available)  
- **Output:** Standardized JSON array of paper details for further processing.

### 3. Analyze Impact & Relevance AI (`OpenAI` Node)

- **Purpose:** Uses OpenAI to perform sentiment and relevance analysis on each paper’s abstract or, if missing, the title.
- **Input:** Abstract text or title of each paper.
- **Output:** AI-generated analysis describing the paper’s impact and relevance.

### 4. Format Analysis Output (`Function`)

- **Purpose:** Converts the raw AI response into a clean structured format containing the paper’s details along with the impact analysis.
- **Output:** JSON objects for each paper, including the AI impact summary.

### 5. Identify Collaboration Opportunities (`Function`)

- **Purpose:** Examines aggregated papers to find potential collaboration opportunities by identifying overlapping authors who have contributed to multiple papers.
- **Logic:**  
  - Builds a mapping of authors to their papers.
  - Detects authors with more than one associated paper indicating collaboration potential.
- **Output:**  
  - List of collaborators with their overlapping papers, or  
  - Message indicating no immediate collaboration opportunities found.

### 6. Notify Collaborators Slack (`Slack` Node)

- **Purpose:** Posts a notification to the `quantum-research-collab` Slack channel about identified collaboration opportunities or the absence thereof.
- **Message:** Includes formatted JSON output summarizing collaborations or a no-opportunity notice.

### 7. Generate Innovation Insights Report (`Function`)

- **Purpose:** Generates a comprehensive report combining:  
  - Impact and relevance assessments of all papers  
  - Summary of collaboration opportunities  
- **Report Format:**  
  - Lists each paper’s title and AI-driven impact assessment.  
  - Provides a summary of authors involved in multiple papers, highlighting innovation and collaboration potential.
- **Output:** A textual report summarizing insights.

### 8. Send Report Notification to Community (`Slack` Node)

- **Purpose:** Sends the Innovation Insights Report to the `quantum-research-community` Slack channel.
- **Message:** The full textual report produced by the previous node.

---

## Credentials Required

- **OpenAI API:** For impact and relevance sentiment analysis.
- **Slack API:** For posting notifications in Slack channels.

---

## Channels Used in Slack

- **quantum-research-collab:** For collaboration opportunity notifications.
- **quantum-research-community:** For sharing full innovation insights reports.

---

## Workflow Connections Summary

- Recent papers fetched → Extract paper details  
- Paper details → Impact & relevance AI analysis  
- AI outputs → Format analysis output  
- Formatted outputs → Identify collaboration opportunities & generate report  
- Collaboration opportunities → Notify collaborators Slack & report generation  
- Generated report → Send report notification to community Slack

---

## Activation

- The workflow is initially inactive (`"active": false`). To use, enable it within your n8n environment.

---

## Use Case

This workflow is designed for research teams, project managers, and innovation coordinators in quantum computing to automate the discovery of new research articles, assess their impact, identify cross-author collaboration potentials, and notify relevant communities efficiently.

---

# End of README.md