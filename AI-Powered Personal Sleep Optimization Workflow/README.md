# AI-Driven Personal Sleep Quality Analysis and Optimization System

This workflow is designed to analyze personal sleep data using AI-driven insights and optimize sleep quality through smart home automation and notifications. It integrates wearable sleep data, AI analysis with GPT-4, and smart home devices to provide actionable recommendations and alerts.

---

## Workflow Overview

### 1. Get Sleep Data
- **Node:** `Get Sleep Data`
- **Type:** Wearable API (OAuth2 Authentication)
- **Function:** Retrieves sleep data from a wearable device or all devices for the current date.
- **Parameters:**
  - `deviceId`: Target device ID or fetches all devices.
  - `startDate` & `endDate`: Both set to today's date (ISO format).

### 2. Preprocess Sleep Data
- **Node:** `Preprocess Sleep Data`
- **Type:** Function
- **Function:** Aggregates sleep data metrics by calculating averages:
  - Average total sleep (in minutes)
  - Average number of awakenings
  - Average sleep latency (in minutes)

### 3. AI Sleep Analysis
- **Node:** `AI Sleep Analysis`
- **Type:** OpenAI GPT-4
- **Function:** Analyzes the average sleep metrics to identify possible sleep issues such as sleep apnea or insomnia.
- **Prompt:** AI acts as a sleep specialist to provide:
  - Diagnosis
  - Recommendations to improve sleep
  - Alerts if any critical conditions are detected
- **Response Format:** JSON with `diagnosis`, `recommendations`, and `alerts`.

### 4. Parse AI Response
- **Node:** `Parse AI Response`
- **Type:** Function
- **Function:** Parses the JSON response from AI to extract insights.

### 5. Generate Smart Home Commands
- **Node:** `Generate Smart Home Commands`
- **Type:** Function
- **Function:** Constructs commands for smart home devices based on AI recommendations and alerts:
  - Dimming lights if reducing light exposure is recommended.
  - Setting thermostat temperature (e.g., 19°C) to optimize bedroom conditions.
  - Playing white noise if frequent awakenings are detected.
- **Output:** JSON commands array and alerts for further actions.

### 6. Send Commands to Smart Home
- **Node:** `Send Commands to Smart Home`
- **Type:** HTTP Request (POST)
- **Function:** Sends the generated smart home commands to the local smart home API endpoint at `http://smarthome.local/api/commands`.

### 7. Send Email Report
- **Node:** `Send Email Report`
- **Type:** Email Send
- **Function:** Sends a detailed personalized sleep analysis report via email.
- **Email Content:**
  - Diagnosis summary
  - Recommendations
  - Alerts
- **Sender:** `sleep.analytics@yourdomain.com`
- **Recipient:** User email from data or fallback `user@example.com`

### 8. Send Slack Notification
- **Node:** `Send Slack Notification`
- **Type:** Slack Post Message
- **Function:** Posts the sleep analysis report to a Slack channel named `sleep-analysis` for team or personal monitoring.

---

## Data Flow Summary

```
Get Sleep Data
      ↓
Preprocess Sleep Data
      ↓
AI Sleep Analysis (GPT-4)
      ↓
Parse AI Response
     ↙       ↘         ↘
Generate  Send Email  Send Slack
Smart Home   Report    Notification
Commands
      ↓
Send Commands to Smart Home
```

---

## Setup Instructions

### Prerequisites
- Access to a wearable device API with OAuth2 authentication providing sleep data.
- OpenAI GPT-4 API access configured in the workflow.
- Local smart home system accessible via HTTP API (`http://smarthome.local/api/commands`).
- Email SMTP configuration for sending reports.
- Slack workspace and bot with permission to post messages in the `sleep-analysis` channel.

### Configuration
- Provide OAuth2 credentials for wearable API.
- Set OpenAI API key with GPT-4 model access.
- Configure the smart home system API endpoint if different.
- Update sender and recipient email addresses as needed.
- Configure Slack credentials and specify correct channel.

---

## AI Response Specification

The AI response JSON must include:
- **diagnosis**: Summary of potential sleep disorders or issues.
- **recommendations**: Actionable advice to improve sleep hygiene and quality.
- **alerts**: Any urgent or notable warnings requiring immediate attention.

Example:
```json
{
  "diagnosis": "Signs of mild insomnia with frequent awakenings.",
  "recommendations": [
    "reduce light exposure before bedtime",
    "keep bedroom around 18-20°C",
    "use white noise to minimize awakenings"
  ],
  "alerts": [
    "monitor sleep apnea symptoms closely"
  ]
}
```

---

## Notes

- The workflow processes only the current day’s sleep data by default but can be adjusted for longer date ranges.
- Smart home commands are examples and can be expanded based on additional recommendations.
- Email and Slack reporting provide multiple channels for user feedback and monitoring.

---

Thank you for using the **AI-Driven Personal Sleep Quality Analysis and Optimization System** to improve your sleep and overall health!