# Smart Urban Garden AI Assistant

## Overview
The **Smart Urban Garden AI Assistant** is an automated workflow designed to monitor and optimize urban garden health using environmental sensor and plant status data. It integrates real-time data fetching, AI-powered analysis, scheduled care routines, and multi-channel alerts to provide eco-friendly gardening recommendations and treatments.

---

## Workflow Components and Functionality

### 1. Define Schedules
- **Node:** `Define Schedules` (Function node)
- **Purpose:** Defines recurring schedules for watering, pest control, and sunlight optimization.
- **Details:**
  - Watering: Every day at 7:00 AM (America/New_York timezone)
  - Pest Control: Mondays and Thursdays at 8:00 AM
  - Sunlight Optimization: Every day at 9:00 AM

### 2. Schedule Trigger
- **Node:** `Schedule Trigger` (Cron node)
- **Purpose:** Triggers workflow executions based on defined cron times and timezones from the schedules.

### 3. Create Treatment Command
- **Node:** `Create Treatment Command` (Function node)
- **Purpose:** Creates commands corresponding to the scheduled treatment type to be executed.

### 4. Execute Treatment Command
- **Node:** `Execute Treatment Command` (HTTP Request node)
- **Purpose:** Sends treatment commands to the plant care system API for execution.
- **API:** `POST https://api.plantcare.local/execute-treatment`

---

### 5. Daily Trigger
- **Node:** `Daily Trigger` (Interval node)
- **Purpose:** Initiates the daily monitoring and analysis process once every 24 hours (86400000 ms).

### 6. Fetch Environmental Sensor Data
- **Node:** `Fetch Environmental Sensor Data` (HTTP Request node)
- **Purpose:** Fetches current environmental data from sensors, including temperature, humidity, soil moisture, etc.
- **API:** `GET https://api.environmentalsensors.local/data`

### 7. Fetch Plant Status Data
- **Node:** `Fetch Plant Status Data` (HTTP Request node)
- **Purpose:** Retrieves plant health status data such as growth condition, disease indicators, and pest presence.
- **API:** `GET https://api.plantcare.local/status`

### 8. Combine Sensor and Plant Data
- **Node:** `Combine Sensor and Plant Data` (Function node)
- **Purpose:** Merges environmental and plant status data into a single structured JSON object for AI analysis.

---

### 9. AI Analysis and Recommendations
- **Node:** `AI Analysis and Recommendations` (OpenAI node)
- **Purpose:** Sends combined data to the GPT-4 model to:
  - Detect pest infestations and diseases.
  - Provide alerts.
  - Generate eco-friendly care recommendations including watering schedules, pest control, and sunlight optimization.
  - Suggest treatment schedules.
- **System Prompt:** Instructs the AI to act as a smart garden assistant.
- **User Prompt:** Includes combined sensor data and requests analysis and recommendations.

### 10. Parse AI Response
- **Node:** `Parse AI Response` (Function node)
- **Purpose:** Processes the AI-generated response to:
  - Extract structured JSON recommendations if provided.
  - Retain raw textual analysis if JSON extraction fails.
- **Output:** Contains both the analyzed text and parsed recommendations.

---

### 11. Send Daily Summary Email
- **Node:** `Send Daily Summary Email` (Email Send node)
- **Purpose:** Emails the daily summary and AI recommendations to the user.
- **Recipient:** `user@example.com`
- **Subject:** "Daily Smart Urban Garden Report"

### 12. Send Real-Time Alerts
- **Node:** `Send Real-Time Alerts` (Slack node)
- **Purpose:** Sends immediate alerts via Slack channel `garden-alerts` for any detected issues requiring urgent attention.

---

## Workflow Execution Flow

1. **Scheduled Treatments:**
   - `Define Schedules` → `Schedule Trigger` → `Create Treatment Command` → `Execute Treatment Command`
   - Triggers specific watering, pest control, or sunlight optimization tasks at predefined times.

2. **Daily Monitoring & Recommendations:**
   - `Daily Trigger` → `Fetch Environmental Sensor Data` → `Fetch Plant Status Data`
   - → `Combine Sensor and Plant Data` → `AI Analysis and Recommendations`
   - → `Parse AI Response` → [`Send Daily Summary Email`, `Send Real-Time Alerts`]

---

## Setup and Configuration Notes

- **APIs:**
  - Ensure access to environmental sensor (`https://api.environmentalsensors.local/data`) and plant care (`https://api.plantcare.local`) APIs.
  - Configure authentication if required by these APIs.

- **OpenAI API:**
  - Configure OpenAI credentials and access for the GPT-4 model.
  - Adjust prompts as needed to fit specific garden types or preferences.

- **Email:**
  - Replace `user@example.com` with the recipient's email address.
  - Ensure email node is configured with valid SMTP or email service credentials.

- **Slack:**
  - Set up Slack node with correct workspace credentials.
  - Ensure the `garden-alerts` Slack channel exists and is accessible.

- **Timezone:**
  - Timezones used (e.g., America/New_York) should be adjusted to local garden location as needed.

---

## Summary
This workflow fully automates urban garden care by integrating data collection, AI-powered analysis, scheduled treatments, and proactive communication to ensure healthy, thriving plants with eco-friendly methods.