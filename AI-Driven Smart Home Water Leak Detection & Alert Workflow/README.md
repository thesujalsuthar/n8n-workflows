# AI-Powered Smart Home Water Leak Detection & Alert System

## Overview
This workflow automates water leak detection in smart homes using AI analysis of sensor data. It retrieves real-time moisture readings from water leak sensors, processes and analyzes the data with an AI model to identify potential leaks or abnormal moisture levels, and sends urgent alerts via email and SMS to the homeowner if a leak is detected.

---

## Workflow Breakdown

### 1. Get Water Leak Sensor Data
- **Node Type:** HTTP Request  
- **Purpose:** Fetches the latest water leak sensor data from the Smart Sensor Provider API.  
- **Details:**  
  - Uses a `GET` request to `https://api.smartsensorprovider.com/v1/water-leak-sensors/data`.  
  - Authenticated via HTTP header with a Bearer token from the `smartSensorApi` credentials.  
  - Response format set to JSON.

### 2. Extract Features for AI
- **Node Type:** Function  
- **Purpose:** Parses raw sensor data and prepares features for AI analysis.  
- **Details:**  
  - Extracts moisture level, sensor ID, and timestamp from each sensor reading.  
  - Maps data into a structured format suitable for input into the AI model.

### 3. AI Leak Detection Analysis
- **Node Type:** OpenAI (GPT-4o-mini)  
- **Purpose:** Performs AI analysis on sensor readings to detect leaks or abnormal moisture levels.  
- **Details:**  
  - The AI assistant is instructed to analyze sensor data, considering historical norms to reduce false alarms.  
  - Responds with a JSON object containing:
    - `leakDetected` (boolean)  
    - `confidence` (number between 0 and 1)  
    - `recommendedAction` (string)  

### 4. Filter Leak Alerts
- **Node Type:** Function  
- **Purpose:** Filters AI responses to only pass through alerts for detected leaks.  
- **Details:**  
  - Parses the AI response, returning leak alerts only if `leakDetected` is `true`.  
  - If no leak is detected, halts further processing.

### 5. Send Email Alert
- **Node Type:** Email Send  
- **Purpose:** Sends an urgent email notification to the homeowner when a leak is detected.  
- **Details:**  
  - Sender email: `alerts@smarthome.com`  
  - Recipient email: homeowner's email from `homeowner` credentials.  
  - Email subject: *Urgent: Water Leak Detected in Your Home*  
  - Email body includes confidence score and recommended action from AI analysis.

### 6. Send SMS Alert
- **Node Type:** Twilio  
- **Purpose:** Sends an SMS alert to the homeowner's phone with leak detection details.  
- **Details:**  
  - Phone number sourced from `homeowner` credentials.  
  - SMS text summarizes detection confidence and recommended action.  
  - Requires configured Twilio credentials (`twilioApi`).

---

## Credentials Required
- **smartSensorApi**
  - Type: HTTP Header Authentication  
  - Fields: API Key (for Bearer token authentication to sensor data API)  
- **homeowner**
  - Type: Custom Credentials  
  - Fields:  
    - Email (to receive email alerts)  
    - Phone Number (to receive SMS alerts)  
- **twilioApi**
  - For sending SMS alerts via Twilio (Account SID and Auth Token configured in n8n).

---

## Execution Flow
1. Retrieve sensor data via API.
2. Extract relevant features for AI.
3. Analyze sensor readings using AI model.
4. If leak detected, filter AI responses.
5. Send notifications (email and SMS) to homeowner.

---

## Notes
- AI reduces false alarms by evaluating sensor data in relation to historical norms.
- Alerts contain recommended actions for the homeowner to promptly address potential leaks.
- System can be extended with additional sensor types or notification channels if needed.

---

# End of Documentation