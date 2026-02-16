# Quantum Computing Research Trend Visualizer & Alert System

This workflow continuously monitors the latest developments in the quantum computing field by fetching recent research papers and news articles, extracting key entities using NLP, aggregating trends, preparing visualization data, and sending email alerts for significant trends.

---

## Workflow Overview

### 1. Fetch Latest Research Papers  
- **Node**: `Fetch Latest Research Papers`  
- **Action**: Queries CrossRef API to retrieve research papers related to *quantum computing* published since the previous day.  
- **Endpoint**: `https://api.crossref.org/works` filtered by subject and recent publication date.  
- **Data Format**: JSON response containing relevant metadata for papers.

### 2. Fetch Latest News  
- **Node**: `Fetch Latest News`  
- **Action**: Pulls news articles published since the previous day containing the keyword *quantum computing* via NewsAPI.  
- **Endpoint**: `https://newsapi.org/v2/everything` with query and date filters.  
- **Authentication**: Requires NewsAPI key from credentials.  
- **Data Format**: JSON response with news metadata.

### 3. Combine and Format Data  
- **Node**: `Combine and Format Data`  
- **Action**:  
  - Takes the two data sources (papers and news).  
  - Extracts relevant fields: unique ID, title, abstract/description, source type, and publication date.  
  - Creates a unified list of entries for downstream processing.

### 4. Extract Named Entities (NLP)  
- **Node**: `Extract Named Entities (NLP)`  
- **Action**:  
  - Uses AWS Comprehend service to detect named entities from combined title and abstract text.  
  - Annotates entries with recognized entities such as technical terms, organizations, dates, and people relevant to quantum computing.

### 5. Aggregate and Rank Trends  
- **Node**: `Aggregate and Rank Trends`  
- **Action**:  
  - Aggregates entity mentions across all entries.  
  - Counts frequency per entity and tracks the latest mention date.  
  - Sorts entities to identify the **top 20 emerging trends** by mention count.

### 6. Prepare Visualization Data  
- **Node**: `Prepare Visualization Data`  
- **Action**:  
  - Formats the top trends information into JSON suitable for visualization tools (e.g. QuickChart).  
  - Creates a bar chart configuration with trend labels and counts, enabling clear, visual trend representation.

### 7. Compose Alerts  
- **Node**: `Compose Alerts`  
- **Action**:  
  - Checks if any discovered trend crosses a predefined threshold (≥ 5 mentions).  
  - Generates alert messages describing each significant trend, including name, count, and recent activity date.  
  - Outputs an alert message for automated notification.

### 8. Send Alert Emails  
- **Node**: `Send Alert Emails`  
- **Action**:  
  - Sends email notifications containing the composed alert messages.  
  - Uses provided email credentials for sender and recipient addresses.  
  - Subject: *Quantum Computing Breakthrough Alert*.

---

## Data Flow Summary

1. **CrossRef API** and **NewsAPI** calls run sequentially, fetching data for the previous day.  
2. The two datasets are unified into a common format combining papers and news articles.  
3. AWS Comprehend analyzes the combined text to extract entities.  
4. Extracted entities are aggregated and ranked by frequency and freshness.  
5. Data is split into:  
   - Visualization data for reporting or dashboards.  
   - Alert messages triggering email notifications if trends are significant.  

---

## Credentials Required

- **NewsAPI**: API key to access news data.  
- **UsersEmails**: List of recipient email addresses for alert distribution.

---

## Trigger and Schedule

- Recommended to run once daily after UTC midnight to capture all documents published the previous day.  
- The workflow automatically calculates "from-pub-date" and "from" parameters as yesterday's date.

---

## Scope and Use Case

- Monitor rapidly evolving research and media landscapes around quantum computing.  
- Automatically detect and highlight emerging topics, breakthroughs, and research trends.  
- Provide visual insights and timely alerts to researchers, professionals, and stakeholders.

---

## Visualization Output Format Example

```json
{
  "chartData": {
    "type": "bar",
    "data": {
      "labels": ["Quantum Supremacy", "Qubits", "Entanglement", "..."],
      "datasets": [{
        "label": "Emerging Quantum Computing Trends",
        "data": [15, 12, 10, "..."],
        "backgroundColor": "rgba(54, 162, 235, 0.7)"
      }]
    },
    "options": {
      "responsive": true,
      "plugins": {
        "legend": { "display": true },
        "title": { "display": true, "text": "Top Quantum Computing Trends" }
      }
    }
  }
}
```

---

## Alert Email Example

```
Subject: Quantum Computing Breakthrough Alert

Significant Quantum Computing Trend detected: "Quantum Supremacy" with 8 mentions. Latest mention on 06/22/2024.
Significant Quantum Computing Trend detected: "Topological Qubits" with 6 mentions. Latest mention on 06/21/2024.
```

---

This workflow provides comprehensive monitoring and alerting capabilities for quantum computing research developments, enabling proactive trend analysis and timely dissemination of critical findings.