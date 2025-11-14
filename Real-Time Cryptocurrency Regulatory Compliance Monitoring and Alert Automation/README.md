# Real-Time Crypto Regulatory Compliance Monitoring and Alert System

## Overview

This workflow automates the process of monitoring cryptocurrency regulatory updates across multiple jurisdictions in real-time, analyzing their potential impact using AI, and notifying compliance teams through Slack while logging the alerts into a CRM system.

---

## Workflow Steps

### 1. Fetch Regulatory Updates

- **Node Type:** HTTP Request
- **Description:** Fetches recent cryptocurrency regulatory updates from the API endpoint `https://api.regulatorydata.example.com/crypto-updates`.
- **Query Parameters:**
  - `jurisdiction`: US, EU, UK, SG, JP
  - `topic`: cryptocurrency
  - `since`: One hour ago from the current time (`{{$moment().subtract(1, 'hour').toISOString()}}`)
- **Response Format:** JSON

---

### 2. Filter Relevant Updates

- **Node Type:** Function
- **Description:** Filters the fetched updates to select only those with a relevance score of 70 or higher.
- **Code Logic:**
  ```javascript
  const updates = items[0].json.data;

  const filteredUpdates = updates.filter(update => update.relevanceScore >= 70);

  return filteredUpdates.map(update => ({ json: update }));
  ```

---

### 3. AI Impact Analysis

- **Node Type:** HTTP Request
- **Description:** Sends the content of each relevant regulatory update to an AI service (`https://api.openai.example.com/v1/engines/impact-analysis/completions`) for detailed impact analysis.
- **HTTP Method:** POST
- **Headers:**
  - Authorization: Bearer token from stored OpenAI API credentials (`{{$credentials.openAIApiKey.apiKey}}`)
  - Content-Type: application/json
- **Body Parameters:**
  - `text`: Content of the regulatory update (`{{$json.content}}`)
- **Response Format:** JSON

---

### 4. Format Alert Message

- **Node Type:** Function
- **Description:** Constructs a detailed alert message combining the regulatory update information and the AI impact analysis results.
- **Message Format:**
  ```
  Regulatory Update Alert:
  Jurisdiction: <jurisdiction>
  Date: <date>
  Summary: <summary>
  Impact Analysis: <AI generated impact analysis text>
  ```

- **Code Logic:**
  ```javascript
  return [{
    json: {
      message: `Regulatory Update Alert:\nJurisdiction: ${items[0].json.jurisdiction}\nDate: ${items[0].json.date}\nSummary: ${items[0].json.summary}\nImpact Analysis: ${items[1].json.choices[0].text}`
    }
  }];
  ```

---

### 5. Send Slack Alert

- **Node Type:** Slack
- **Description:** Sends the formatted alert message to the Slack channel `compliance-alerts`.
- **Channel:** `compliance-alerts`
- **Message Text:** Uses the formatted message from the previous node (`{{$json.message}}`)
- **Credentials:** Slack API token configured in workflow credentials (`slackApi`)

---

### 6. Log Alert to CRM

- **Node Type:** HTTP Request
- **Description:** Logs the alert details into the CRM system for record-keeping and further compliance management.
- **HTTP Method:** POST
- **URL:** `https://api.crmplatform.example.com/v1/alerts`
- **Headers:**
  - Authorization: Bearer token from stored CRM API credentials (`{{$credentials.crmApi.apiKey}}`)
  - Content-Type: application/json
- **Body:**
  ```json
  {
    "jurisdiction": "<jurisdiction>",
    "date": "<date>",
    "summary": "<summary>",
    "impact": "<AI generated impact analysis>"
  }
  ```
  Values are dynamically populated from the node data.

---

## Connections Flow

1. **Fetch Regulatory Updates** → 2. **Filter Relevant Updates**
2. **Filter Relevant Updates** → 3. **AI Impact Analysis**
3. **AI Impact Analysis** → 4. **Format Alert Message**
4. **Format Alert Message** → 5. **Send Slack Alert** and 6. **Log Alert to CRM**

---

## Credentials Needed

- **OpenAI API Key:** For performing AI-based impact analysis.
- **Slack API Token:** To post compliance alerts to the Slack channel.
- **CRM API Key:** To log alerts into the CRM platform.

---

## Usage

- Activate the workflow within your n8n environment.
- Ensure all credentials are correctly set up.
- The workflow will run periodically to fetch updates from the last hour.
- Relevant updates will trigger AI analysis and produce alerts automatically.
- Compliance alerts will be sent to Slack and logged into the CRM system seamlessly.

---

## Notes

- The jurisdictions monitored include the United States (US), European Union (EU), United Kingdom (UK), Singapore (SG), and Japan (JP).
- The relevance threshold is set at 70 to minimize noise and focus on high-impact updates.
- The AI model endpoint and CRM URLs are placeholders and should be replaced with actual service endpoints.