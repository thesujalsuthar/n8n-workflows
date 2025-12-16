# EcoGarden AI Pest Management Scheduler

## Overview
This workflow automates the detection and management of pest issues in your garden using IoT sensor data and AI analysis. Upon detecting a pest problem, it schedules an eco-friendly pest treatment task based on current weather conditions and alerts the gardener via email and SMS.

---

## Workflow Nodes and Functionality

### 1. Fetch Sensor Data
- **Node Type:** HTTP Request  
- **Purpose:** Retrieves IoT garden sensor data from a specified device.
- **Parameters:**
  - `deviceId`: Your IoT sensor device ID.
  - `resource`: `data`
  - `operation`: `get`
- **Authentication:** HTTP Basic Auth using sensor API credentials (`sensor_api_user` / `sensor_api_password`).

---

### 2. AI Pest Detection
- **Node Type:** OpenAI
- **Purpose:** Analyzes the IoT sensor data to detect the presence, type, and severity of pest infestation.
- **Model:** `text-davinci-003`
- **Prompt:**  
  ```  
  You are an AI that analyzes IoT garden sensor data to detect pest severity levels.  
  Input JSON data: {{ $json }}.  
  Output JSON with fields: { pestDetected: boolean, severity: 'low' | 'medium' | 'high', pestType: string, recommendedTreatment: string }.  
  Analyze and provide the output accordingly.  
  ```
- **Response:** JSON object containing pest detection results and treatment recommendations.

---

### 3. If Pest Detected
- **Node Type:** If
- **Purpose:** Checks if the AI has detected pest presence (`pestDetected` is true).
- **Branches:**
  - **True:** Proceed to weather check.
  - **False:** End workflow.

---

### 4. Get Current Weather
- **Node Type:** Weather
- **Purpose:** Fetches current weather conditions at your garden location.
- **Parameters:**
  - `location`: Your garden location.

---

### 5. If Weather Suitable
- **Node Type:** If
- **Purpose:** Checks if the weather is suitable for pest treatment (i.e., weather condition does **not** contain "rain").
- **Branches:**
  - **True:** Proceed to schedule treatment.
  - **False:** End workflow (postpone treatment).

---

### 6. Schedule Treatment Task
- **Node Type:** Google Calendar
- **Purpose:** Creates an event on your Google Calendar for the eco-friendly pest treatment.
- **Parameters:**
  - `text`: Treatment description including recommended treatment.
  - `startDateTime`: Current date-time.
  - `endDateTime`: One hour after the start time.
  - `details`: Includes pest type, severity, and weather info.
- **Authentication:** Google Calendar OAuth2 connection (`google_calendar_account`).

---

### 7. Send Email Reminder
- **Node Type:** Email Send
- **Purpose:** Sends an email notification to the gardener about the pest detection and scheduled treatment.
- **Parameters:**
  - `fromEmail`: `eco.garden@example.com`
  - `toEmail`: Gardener’s email (`gardenerEmail` from JSON data)
  - `subject`: "EcoGarden AI Pest Management Reminder"
  - `text`: Email body with pest detection details and treatment info.
- **Authentication:** SMTP credentials (`your_smtp_credential`).

---

### 8. Send SMS Reminder
- **Node Type:** Twilio
- **Purpose:** Sends an SMS alert to the gardener’s phone.
- **Parameters:**
  - `to`: Gardener’s phone number (`gardenerPhone` from JSON data)
  - `text`: Brief alert message about pest detection and treatment scheduling.
- **Authentication:** Twilio API credentials (`twilio_account`).

---

### 9. Log Action
- **Node Type:** Google Sheets
- **Purpose:** Logs the pest detection event and treatment scheduling details in a Google Sheet.
- **Parameters:**
  - `resource`: `logs`
  - `operation`: `append`
  - `data`: JSON with action details, pest type, severity, treatment, scheduled time, and weather.
- **Authentication:** Google Sheets OAuth2 connection (`google_sheets_account`).

---

## Workflow Connections and Flow
1. **Fetch Sensor Data** → **AI Pest Detection**  
2. **AI Pest Detection** → **If Pest Detected**  
3. If pest detected:  
   - → **Get Current Weather**  
   - → **If Weather Suitable**  
   - If weather suitable:  
     - → **Schedule Treatment Task**  
     - → **Send Email Reminder** and **Send SMS Reminder** (parallel)  
     - **Send Email Reminder** → **Log Action**  
4. If no pest detected or weather unsuitable: workflow ends without further actions.

---

## Setup Instructions

1. Replace placeholders with your actual details:
   - `your_iot_sensor_device_id`
   - `sensor_api_user` and `sensor_api_password`
   - `your_garden_location`
   - Email and phone fields sourced dynamically from sensor data or input.
2. Configure credentials in n8n:
   - HTTP Basic Auth for sensor API
   - OpenAI API key for AI analysis
   - Google Calendar OAuth2 for event scheduling
   - SMTP credentials for sending emails
   - Twilio API credentials for SMS
   - Google Sheets OAuth2 for logging
3. Activate the workflow in the n8n environment.

---

## Summary
This end-to-end workflow enables automated monitoring, AI-based analysis, scheduling, and notification for pest management in your garden, incorporating current environmental conditions to optimize treatment timing and ensure eco-friendly practices.