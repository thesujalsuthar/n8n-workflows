# AI-Powered Smart Home Energy Usage Optimizer and Alert System

## Overview

This workflow is designed to optimize and monitor smart home energy consumption in near real-time. It fetches energy data every 5 minutes, uses AI to analyze consumption patterns, generates alerts for unusual usage, and provides actionable optimization recommendations.

---

## Workflow Summary

1. **Scheduled Trigger (Every 5 Minutes Trigger)**  
   Initiates workflow execution every 5 minutes to ensure continuous monitoring.

2. **Fetch Real-Time Energy Data**  
   Retrieves current energy consumption metrics, historical trends, and device-specific usage from the smart home API (`http://smart-home-api.local/energy/consumption`).

3. **Prepare Data for AI**  
   Processes and formats the fetched data into a structured JSON object ready for AI analysis. This includes:
   - Current total usage
   - Historical usage data (timestamped)
   - Device-wise energy usage breakdown

4. **AI Energy Usage Analyzer**  
   Sends the prepared energy data to an AI model (`gpt-4o-mini`) with a prompt to:
   - Identify unusual or abnormal consumption patterns
   - Generate actionable energy optimization recommendations  
   
   The AI returns a JSON object containing two arrays:
   - `alerts`: List of current unusual usage alerts
   - `recommendations`: Suggestions for energy savings

5. **Parse AI Response**  
   Parses the AI's JSON response string into a usable JSON object for downstream processing.

6. **Prepare Alert Messages**  
   Converts each alert from the AI into a formatted textual alert message prefixed with "⚠️ Alert:".  
   If no alerts are present, this step ends with no outputs.

7. **Email Alert Sender**  
   Sends alert messages via email from `alerts@smarthome.local` to the user (`user@smarthome.local`).  
   - Subject: "Unusual Energy Usage Alert"  
   - Body: Contains the alert message text

8. **Format Recommendations**  
   Formats the optimization recommendations into a human-readable numbered list with the header "Optimization Recommendations:".

9. **Send Recommendations to Dashboard**  
   Posts the formatted recommendations to a smart home dashboard API (`http://smart-home-dashboard.local/api/energy-optimization`) via a POST request for display and action.

---

## Detailed Node Description

### 1. Every 5 Minutes Trigger
- **Type:** Cron
- **Schedule:** Every 5 minutes (00, 05, 10, ..., 55 minutes hour).
- **Purpose:** Start the workflow periodically.

### 2. Fetch Real-Time Energy Data
- **Type:** HTTP Request (GET)
- **URL:** `http://smart-home-api.local/energy/consumption`
- **Response Format:** JSON
- **Purpose:** Get current and historical energy usage data from the smart home system.

### 3. Prepare Data for AI
- **Type:** Function
- **Code:** Extracts and organizes the key data fields (`currentUsage`, `historicalData`, `deviceUsages`) for AI processing.

### 4. AI Energy Usage Analyzer
- **Type:** OpenAI Node
- **Model:** GPT-4o-mini
- **Prompt:**  
  Instructs AI to analyze JSON energy data for suspicious patterns and optimization tips and to respond with JSON containing:
  - `alerts` (array of strings)  
  - `recommendations` (array of strings)

### 5. Parse AI Response
- **Type:** Function
- **Purpose:** Parses AI response string content into a JSON object; handles errors by returning empty alerts/recommendations with an error message.

### 6. Prepare Alert Messages
- **Type:** Function
- **Purpose:** For each alert string, create an alert message prefixed with a warning emoji. No output if no alerts.

### 7. Email Alert Sender
- **Type:** Email Send
- **From:** alerts@smarthome.local  
- **To:** user@smarthome.local  
- **Subject:** Unusual Energy Usage Alert  
- **Body:** Text from prepared alert messages.  
- **Purpose:** Notify user immediately of unusual energy consumption.

### 8. Format Recommendations
- **Type:** Function
- **Purpose:** Converts AI recommendations array into a numbered list text for user-friendly display.

### 9. Send Recommendations to Dashboard
- **Type:** HTTP Request (POST)
- **URL:** `http://smart-home-dashboard.local/api/energy-optimization`  
- **Body:** JSON containing the formatted recommendations  
- **Purpose:** Updates smart home dashboard with actionable energy saving tips.

---

## Usage and Setup

1. Deploy this workflow in your n8n instance.
2. Configure API endpoints to match your smart home infrastructure and dashboard.
3. Set correct email credentials and permissions for sending alerts.
4. Provide an OpenAI API key with access to the GPT-4o-mini model.
5. Start the workflow to enable continuous 5-minute interval monitoring and optimization.

---

## Expected Data Formats

### Energy Data Sample (from smart home API)
```json
{
  "currentUsage": 350,
  "historicalData": [
    {"timestamp": "2024-06-01T12:00:00Z", "usage": 300},
    {"timestamp": "2024-06-01T12:05:00Z", "usage": 340}
  ],
  "deviceUsages": [
    {"deviceId": "AC01", "usage": 120},
    {"deviceId": "Heater02", "usage": 90},
    {"deviceId": "Lights03", "usage": 50}
  ]
}
```

### AI Output Example
```json
{
  "alerts": [
    "Unusually high energy usage detected for Heater02 in the last hour."
  ],
  "recommendations": [
    "Consider reducing Heater02 usage during non-peak hours.",
    "Use smart thermostat settings to optimize AC01 runtime."
  ]
}
```

---

## Benefits

- **Proactive Energy Management:** Automated detection and alerting for abnormal consumption.
- **Cost Savings:** Personalized recommendations to optimize energy use.
- **User Convenience:** Alerts via email and dashboard integration for easy monitoring.
- **Continuous Operation:** Runs every 5 minutes to provide up-to-date insights.

---

## License

This workflow is provided as-is for personal or organizational use. Modify endpoints and credentials as needed for your environment.

---

Thank you for using the AI-Powered Smart Home Energy Usage Optimizer and Alert System!