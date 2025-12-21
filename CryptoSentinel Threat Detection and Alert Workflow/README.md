# CryptoSentinel Workflow

This workflow is designed to monitor cryptocurrency markets and blockchain networks for suspicious activities, potential hacks, and regulatory changes. It aggregates data from multiple sources, performs AI-driven threat analysis, and sends timely alerts via email, Slack, and SMS.

---

## Workflow Overview

### 1. Fetch Exchange Data
- **Node Name:** Fetch Exchange Data
- **Type:** HTTP Request
- **Description:** Retrieves real-time market data from a cryptocurrency exchange API.
- **Details:**
  - **Endpoint:** `https://api.cryptoexchange1.com/v1/market_data`
  - **Method:** GET
  - **Authentication:** Bearer Token (Crypto Exchange API Credentials)
  - **Response Format:** JSON

### 2. Fetch Blockchain Events
- **Node Name:** Fetch Blockchain Events
- **Type:** HTTP Request
- **Description:** Fetches blockchain event data filtered for suspicious activities.
- **Details:**
  - **Endpoint:** `https://api.blockchainnetwork.com/v1/events`
  - **Method:** GET
  - **Query Parameter:** `eventType = suspicious_activity`
  - **Response Format:** JSON

### 3. Fetch Regulatory News
- **Node Name:** Fetch Regulatory News
- **Type:** HTTP Request
- **Description:** Gathers news articles related to cryptocurrency regulations and hacks.
- **Details:**
  - **Endpoint:** `https://newsapi.org/v2/everything`
  - **Method:** GET
  - **Query Parameters:**
    - `q = cryptocurrency regulation hack`
    - `apiKey` (News API Credentials)
  - **Response Format:** JSON
  - **Authentication:** API Key

### 4. Combine Data
- **Node Name:** Combine Data
- **Type:** Merge
- **Description:** Merges the data fetched from the exchange, blockchain events, and news into a single dataset for analysis.
- **Parameters:** Pass-through merge of JSON responses into a combined `data` property.

### 5. AI Threat Analysis
- **Node Name:** AI Threat Analysis
- **Type:** OpenAI (Chat Completion)
- **Description:** Uses OpenAI’s GPT-4 model to analyze combined data and detect threats.
- **Instructions to AI:**
  - Analyze combined data from exchanges, blockchain events, and news.
  - Identify suspicious activities, potential hacks, and regulatory changes.
  - Output a concise, actionable alert summary including threat levels.
- **Model:** GPT-4
- **Temperature:** 0.2 (for focused, accurate responses)
- **Authentication:** OpenAI API Key

### 6. Format Alert Message
- **Node Name:** Format Alert Message
- **Type:** Function
- **Description:** Extracts the AI-generated analysis message and formats it as an alert message.
- **Code:**
  ```javascript
  const analysis = items[0].json.choices[0].message.content;
  return [{ json: { alertMessage: analysis }}];
  ```

### 7. Send Email Alert
- **Node Name:** Send Email Alert
- **Type:** Email Send
- **Description:** Sends the alert summary via email.
- **From:** `alerts@cryptosentinel.com`
- **To:** User email address or default `user@example.com`
- **Subject:** `CryptoSentinel Alert: Suspicious Activity Detected`
- **Content:** AI-generated alert message
- **Authentication:** SMTP Credentials

### 8. Send Slack Alert
- **Node Name:** Send Slack Alert
- **Type:** Slack Message
- **Description:** Posts the alert message to a designated Slack channel.
- **Channel:** `#crypto-alerts`
- **Message:** AI-generated alert message
- **Authentication:** Slack API

### 9. Send SMS Alert
- **Node Name:** Send SMS Alert
- **Type:** Twilio
- **Description:** Sends SMS alerts containing the AI-generated message.
- **Recipient:** User phone number or default `+1234567890`
- **Message:** AI-generated alert message
- **Authentication:** Twilio API

---

## Credentials Required

| Credential Name               | Purpose                          |
|------------------------------|---------------------------------|
| Crypto Exchange API Credentials | Authentication for exchange API  |
| News API Credentials           | Authentication for news API      |
| OpenAI API                    | Access to GPT-4 model            |
| SMTP Credentials             | Sending email alerts             |
| Slack API                    | Sending Slack alerts             |
| Twilio API                   | Sending SMS alerts               |

---

## Workflow Connections

- **Nodes 1, 2, 3** feed data into **Node 4 (Combine Data)**.
- **Node 4** sends combined data to **Node 5 (AI Threat Analysis)**.
- **Node 5** results are passed to **Node 6 (Format Alert Message)**.
- **Node 6** triggers three alert delivery nodes simultaneously:
  - **Node 7 (Send Email Alert)**
  - **Node 8 (Send Slack Alert)**
  - **Node 9 (Send SMS Alert)**

---

## Activation

- The workflow is active and ready to operate.
- Designed for continuous monitoring and instant alert dissemination.

---

## Summary

This workflow provides an integrated monitoring and alerting system for the cryptocurrency sector by combining exchange data, blockchain event monitoring, and real-time news analysis powered by AI. Alerts are distributed via multiple communication channels to ensure timely awareness and response.