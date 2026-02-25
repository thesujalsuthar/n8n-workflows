# AI-Enhanced Community Water Usage Monitoring & Conservation Alert System

## Overview

This workflow automates daily monitoring of community water usage by fetching real-time data, analyzing it with AI to detect anomalies, and sending personalized conservation alerts to residents. It also updates a dashboard database to provide ongoing insights into water consumption patterns.

---

## Workflow Schedule

- **Trigger:** The workflow runs **daily at 8:00 AM**.

---

## Workflow Nodes & Detailed Description

### 1. Scheduled Trigger
- **Type:** Trigger node
- **Function:** Starts the workflow every day at 8:00 AM to initiate the data collection and analysis process.

### 2. Fetch Real-Time Water Usage Data
- **Type:** HTTP Request
- **Function:** Retrieves real-time water usage data for the community from the API endpoint:  
  `https://api.communitywaterdata.local/usage/realtime`
- **Headers:** Requires an `Authorization` header with a Bearer API key (`YOUR_API_KEY`).
- **Response Format:** JSON containing water usage statistics and community information.

### 3. Prepare AI Input Payload
- **Type:** Function node
- **Function:** Extracts and formats the water usage data and community ID into a simplified JSON payload suitable for AI analysis.
- **Data Prepared:**
  - `communityId`: Identifier for the community (defaulted to `'community_001'` if absent)
  - `timestamp`: Current timestamp of data retrieval
  - `usage`: The raw usage data fetched from the API

### 4. AI Analyze Usage Patterns
- **Type:** OpenAI Node (GPT-4)
- **Function:** Sends the usage data to the AI model with a prompt to:
  - Analyze usage patterns
  - Detect any unusual or anomalous consumption behavior
  - Provide a summary of detected issues (if any)
  - Generate personalized, friendly water-saving recommendations
- **Response Format:** JSON structured as follows:
  ```json
  {
    "anomaly_detected": true/false,
    "summary": "...",
    "recommendations": ["...", "..."]
  }
  ```
- **Parameters:**  
  - `maxTokens`: 400  
  - `temperature`: 0.7

### 5. Parse AI Response & Filter Anomalies
- **Type:** Function node
- **Function:** Parses the AI response JSON.  
- **Logic:**  
  - If `anomaly_detected` is `false`, the workflow ends here (no alerts sent).  
  - If `true`, proceeds to contact retrieval and messaging.

### 6. Fetch Residents Contact List
- **Type:** CRM Node
- **Function:** Retrieves the contact list of all residents including their:
  - `id`
  - `name`
  - `email`
  - `phone`
  - `appUserId` (for sending app push notifications)

### 7. Generate Personalized Messages
- **Type:** Function node
- **Function:** Creates individualized alert messages for each resident, including:
  - Greeting by name
  - Summary of anomaly detected
  - List of personalized water-saving recommendations formatted as numbered tips

### 8. Send Email Alerts
- **Type:** Email Send node
- **Function:** Sends the personalized messages to each resident's email address.  
- **Email Parameters:**  
  - From: `community@waterconserve.org`  
  - Subject: `"Important: Water Usage Alert & Personalized Conservation Tips"`

### 9. Send SMS Alerts
- **Type:** Twilio Node
- **Function:** Sends the personalized messages via SMS to residents’ phone numbers.

### 10. Send App Notifications
- **Type:** Push Notification Node
- **Function:** Sends push notifications with the alert message to residents’ app user IDs.  
- **Deep Link:** `app://water-usage-dashboard` to direct users to the water usage dashboard within the app.

### 11. Prepare Dashboard Data
- **Type:** Set node
- **Function:** Structures anomaly detection results and recommendations for dashboard indexing:
  - `anomaly_detected` (boolean)
  - `summary` (string)
  - `recommendations` (JSON array)

### 12. Index Data to Dashboard DB
- **Type:** Elasticsearch node
- **Function:** Appends the prepared data to the `community-water-usage` index in the dashboard database to support historical tracking and visualization.

### 13. No Anomaly Detected - End
- **Type:** No-Op node
- **Function:** Terminates workflow gracefully when no anomaly is detected.

---

## Summary of Workflow Execution

1. **Daily Trigger at 8 AM** →  
2. Fetch live water usage data →  
3. Prepare and send data to AI (GPT-4) for anomaly detection →  
4. AI returns detection result and recommendations →  
5. If anomaly exists:  
   - Retrieve residents contacts  
   - Generate personalized water-saving messages  
   - Send alerts via Email, SMS, and App Notifications  
   - Update dashboard database with alert data  
6. If no anomaly, the workflow ends.

---

## Configuration & Setup Notes

- Replace `"YOUR_API_KEY"` in the **Fetch Real-Time Water Usage Data** node with your actual API key.
- Ensure API endpoints and CRM node settings reflect your actual data sources and contact management system.
- Configure email sending node with valid SMTP credentials and authorized sending address.
- Set up Twilio credentials for SMS delivery.
- Push notification node requires valid app user IDs and configured notification service.
- Elasticsearch node URL and credentials must be set according to your dashboard database access.

---

## Benefits

- **Automated Monitoring:** Enables continuous, scheduled tracking of community water usage without manual intervention.
- **AI-Powered Insights:** Leverages GPT-4 to detect anomalies and generate clear, practical recommendations.
- **Personalized Outreach:** Sends tailored alerts to residents via multiple communication channels ensuring high engagement.
- **Data-Driven Conservation:** Empowers communities with timely, actionable guidance to reduce water waste.
- **Dashboard Integration:** Provides comprehensive logging and visualization capabilities for ongoing monitoring by administrators.

---

## License

This workflow and its components are provided "as-is" for community water conservation purposes. Modify and extend according to your organizational needs.

---

*End of documentation*