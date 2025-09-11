# AI-Driven Blockchain Voting Integrity Verification System

This workflow is designed to ensure the integrity of blockchain-based voting transactions by verifying each transaction through AI-driven anomaly detection, logging, alerting suspicious activities to administrators, and generating periodic anomaly reports.

---

## Workflow Overview

The system processes incoming voting transactions, performs integrity checks, scores anomalies, stores results, alerts on suspicious activities, and provides real-time status updates.

---

## Nodes Detail

### 1. Receive Voting Transactions (`Receive Voting Transactions`)
- **Type:** Webhook (HTTP POST)
- **Path:** `/voting-transactions-webhook`
- **Purpose:** Entry point for incoming voting transaction data submitted via POST requests.

### 2. Parse Incoming Transactions (`Parse Incoming Transactions`)
- **Type:** Function
- **Operation:** Parses the incoming JSON payload to JSON format to be used in subsequent nodes.

### 3. Basic Transaction Integrity Check (`Basic Transaction Integrity Check`)
- **Type:** Function
- **Function:**
  - Validates that the transaction object contains mandatory fields: `hash`, `from`, `to`, `value`, and `timestamp`.
  - Ensures transaction structure integrity before further processing.
  - Placeholder for integrating connections to multiple blockchain platforms (Ethereum, Binance Smart Chain, etc.).

### 4. Anomaly Detection Scoring (`Anomaly Detection Scoring`)
- **Type:** Function
- **Function:**
  - Assigns an anomaly score based on heuristics.
  - Example heuristic: votes with values over 1000 are marked as suspicious (score +5).
  - Additional heuristics or AI API calls can be integrated here.
  - Outputs the transaction enriched with an `anomalyScore`.

### 5. Store Transaction Record (`Store Transaction Record`)
- **Type:** MongoDB Insert Document
- **Collection:** `voting_transactions`
- **Purpose:** Persists every processed transaction with its anomaly score and details into MongoDB.

### 6. Check for High Anomaly (`Check for High Anomaly`)
- **Type:** Function
- **Function:**
  - Filters transactions with `anomalyScore` greater than 3.
  - Only high anomaly transactions proceed to the next step.

### 7. Notify Admins (`Notify Admins`)
- **Type:** Slack
- **Channel:** `admins-alerts`
- **Purpose:** Sends alerts for suspicious transactions to administrators via Slack.
- **Message Includes:**
  - Transaction Hash
  - From Address
  - To Address
  - Value
  - Timestamp
  - Anomaly Score

### 8. Fetch All Transactions (`Fetch All Transactions`)
- **Type:** MongoDB Query
- **Collection:** `voting_transactions`
- **Purpose:** Retrieves all voting transaction records from the database for analytics.

### 9. Analyze Anomaly Summary (`Analyze Anomaly Summary`)
- **Type:** Function
- **Function:**
  - Aggregates and counts transactions with anomaly scores greater than 3.
  - Creates a summary object containing count and suspicious transactions details.

### 10. Generate Anomaly Report (`Generate Anomaly Report`)
- **Type:** Email Send
- **Purpose:** Sends an anomaly summary report via email using the `blockchain-voting-report` template.
- **Uses:** SMTP credentials to deliver emails.

### 11. Real-time Updates Webhook (`Real-time Updates Webhook`)
- **Type:** Webhook (HTTP GET)
- **Path:** `/real-time-transaction-update`
- **Purpose:** Endpoint for checking system operational status and real-time transaction monitoring availability.

### 12. Send Real-time Status (`Send Real-time Status`)
- **Type:** Function
- **Function:** Returns JSON status confirming that real-time transaction monitoring is operational with current timestamp.

---

## Workflow Connections

- Incoming transactions flow from webhook → parser → integrity check → scoring → storage.
- High anomaly transactions branch to send Slack alerts.
- Periodic retrieval of all transactions leads to analysis and report generation via email.
- Real-time status can be fetched separately through a dedicated GET webhook.

---

## Prerequisites

- MongoDB database configured for storing `voting_transactions`.
- Slack workspace configured with an app and credentials for sending alerts.
- SMTP email credentials for sending reports.
- Ethers.js or similar libraries available for blockchain interaction in functions.
- API endpoint for submitting voting transactions (POST /voting-transactions-webhook).
- Accessible GET endpoint for real-time status checks.

---

## Deployment & Usage

1. Import this workflow into your n8n instance.
2. Set up the required credentials:
   - Slack API Credential
   - SMTP Email Credential
3. Configure MongoDB node credentials with your database details.
4. Deploy and activate the workflow.
5. Submit voting transactions as JSON POST requests to `/voting-transactions-webhook`.
6. Monitor alerts on Slack and review anomaly reports received by email.
7. Use GET requests on `/real-time-transaction-update` to verify system health.

---

## Transaction Data Format Example

```json
{
  "hash": "0x123abc...",
  "from": "0xabc123...",
  "to": "0xdef456...",
  "value": "1500",
  "timestamp": "2024-05-01T12:34:56Z"
}
```

All fields are required for processing and anomaly analysis.

---

## Summary

This system applies AI-powered anomaly detection to blockchain voting transactions for increased transparency and integrity, achieving automatic alerting and detailed reporting by integrating blockchain data verification with modern communication platforms like Slack and email.