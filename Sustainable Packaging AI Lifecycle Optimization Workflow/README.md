# AI-Powered Sustainable Packaging Lifecycle Tracker and Optimization Workflow

## Overview

This workflow automates the tracking, analysis, and optimization of sustainable packaging lifecycles by integrating lifecycle data retrieval, AI-driven sustainability recommendations, database updates, and team notifications. It operates on a daily schedule to ensure up-to-date monitoring and actionable insights for minimizing environmental impact.

---

## Workflow Components

### 1. Daily Trigger
- **Type:** Schedule Trigger
- **Schedule:** Executes every day at 09:00 AM.
- **Purpose:** Automatically starts the workflow daily to process packaging lifecycle data and generate updated sustainability recommendations.

---

### 2. Get Packaging Lifecycle Data
- **Type:** HTTP Request
- **API Endpoint:** `https://api.example.com/packages/lifecycle/status`
- **Functionality:** Fetches lifecycle data for a specific package using the `packageId`.
- **Input Parameters:** 
  - `packageId` (dynamic, passed from input JSON)
- **Output:** Raw lifecycle data including stages such as procurement, production, distribution, usage, and end-of-life.

---

### 3. Transform Lifecycle Data
- **Type:** Function
- **Functionality:** Processes and restructures the retrieved lifecycle data to extract relevant stages for analysis.
- **Output Structure:**
  ```json
  {
    "packageId": "...",
    "procurement": "...",
    "production": "...",
    "distribution": "...",
    "usage": "...",
    "endOfLife": "..."
  }
  ```

---

### 4. Generate AI Recommendations
- **Type:** OpenAI (GPT-4o-mini model)
- **Prompt:** The AI is presented with packaging lifecycle data and tasked as a sustainability expert to:
  - Provide actionable recommendations aimed at minimizing environmental footprint.
  - Return a JSON object containing:
    - `recommendations`: An array of suggestion strings.
    - `summaryImpactScore`: A numerical score from 0 to 100, where lower indicates better environmental impact.
- **Example Input to AI:**
  ```json
  {
    "packageId": "...",
    "procurement": "...",
    "production": "...",
    "distribution": "...",
    "usage": "...",
    "endOfLife": "..."
  }
  ```

---

### 5. Save Recommendations to DB
- **Type:** PostgreSQL (Upsert operation)
- **Table:** `sustainability_reports`
- **Columns Updated:**
  - `package_id`: Identifier for the packaging.
  - `recommendations`: Serialized JSON string of the AI-generated recommendations.
  - `impact_score`: The AI-generated summary impact score.
  - `last_updated`: Timestamp of latest update (ISO format).
- **Purpose:** Stores or updates sustainability recommendations and impact scores in the database to maintain a historical and current record.

---

### 6. Notify Team via Slack
- **Type:** Slack
- **Channel:** `#sustainability-alerts`
- **Message Format:**
  ```
  Package ID: <packageId>
  Impact Score: <summaryImpactScore>
  Recommendations:
  - <Recommendation 1>
  - <Recommendation 2>
  - ...
  ```
- **Purpose:** Immediately informs the sustainability team of the latest AI recommendations and impact assessments for quick review and action.

---

## Workflow Execution Order

1. **Daily Trigger** activates the workflow every day at 09:00 AM.
2. **Get Packaging Lifecycle Data** pulls the latest lifecycle data for the package.
3. **Transform Lifecycle Data** restructures this data for analysis.
4. **Generate AI Recommendations** uses the AI model to produce optimized sustainability suggestions and impact score.
5. **Save Recommendations to DB** upserts this information into the Postgres database.
6. **Notify Team via Slack** sends an alert with key details to the sustainability Slack channel.

---

## Prerequisites & Configuration

- **API Access:** Valid API credentials and access for `https://api.example.com/packages/lifecycle/status`.
- **PostgreSQL Credentials:** Properly configured credentials for the database connection named `Postgres Credentials`.
- **Slack API Token:** Valid Slack API credentials named `Slack API` for posting messages.
- **n8n Environment:** This workflow is designed to run on the n8n automation platform.
- **OpenAI API:** Access to the GPT-4o-mini model for generating recommendations.

---

## Notes

- The `packageId` must be provided as input in the workflow to fetch lifecycle data.
- Impact scores allow prioritization, with lower scores representing better sustainability performance.
- Recommendations are designed as actionable, focused steps to reduce environmental impact across the packaging lifecycle stages.

---

This workflow enables organizations to continuously monitor and improve the sustainability of their packaging operations efficiently using AI-powered insights and automated communication.