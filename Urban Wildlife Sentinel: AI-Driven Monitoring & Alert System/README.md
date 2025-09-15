# Smart Urban Wildlife Monitoring and Conservation Alert System

## Overview

This workflow automates the monitoring of urban wildlife and their habitats by integrating IoT sensor data, AI-based pattern analysis, and alert notifications. It runs daily at midnight to fetch the latest sensor data, analyze for wildlife presence and potential environmental threats, store results, and notify relevant stakeholders if any threats are detected.

---

## Workflow Nodes and Description

### 1. Schedule Trigger  
- **Type:** Schedule Trigger  
- **Function:** Initiates the workflow every day at 00:00 (midnight).  
- **Position:** `[100, 300]`  

### 2. Fetch IoT Sensor Data  
- **Type:** HTTP Request  
- **Function:** Retrieves the latest data from the IoT platform that monitors urban wildlife environments.  
- **Request Details:**  
  - Method: GET  
  - URL: `https://api.iotplatform.example.com/data/latest`  
  - Headers: Authorization with Bearer token from credentials (`iotApi.apiKey`).  
- **Position:** `[350, 300]`  

### 3. Parse and Prepare Sensor Data  
- **Type:** Function  
- **Function:**  
  - Parses the raw IoT data payload.  
  - Extracts key elements such as `wildlife_signals` (animal detections) and `habitat_conditions` (environmental metrics).  
  - Prepares these structured data points for AI analysis.  
- **Code Snippet:**  
  Extracts `wildlifeSignals` and `habitatConditions` from raw data and returns them as JSON.  
- **Position:** `[600, 300]`

### 4. AI Pattern Analysis  
- **Type:** OpenAI (text-davinci-003)  
- **Function:**  
  - Analyzes wildlife signals and habitat conditions using an AI model.  
  - Detects species presence, activity patterns, and potential environmental threats (e.g., pollution, habitat degradation, human interference).  
  - Generates a structured JSON with detected species, threat types, severity, and suggested conservation actions.  
- **Prompt:** Passes extracted signals and conditions with instructions to respond in JSON.  
- **Parameters:** Temperature 0.5, Max Tokens 512.  
- **Position:** `[900, 300]`  

### 5. Parse AI Response  
- **Type:** Function  
- **Function:** Safely parses the JSON formatted AI response text.  
- **Error Handling:** Returns an error object if parsing fails.  
- **Position:** `[1150, 300]`

### 6. Check for Threats  
- **Type:** If (Boolean Condition)  
- **Function:** Checks if there are any detected threats in the AI analysis output. Proceeds only if the `threats` array exists and contains one or more elements.  
- **Position:** `[1400, 300]`

### 7. Notify Conservationists  
- **Type:** Telegram  
- **Function:** Sends alert messages to conservationists when threats are detected.  
- **Message Includes:**  
  - Threat detected alert header.  
  - List of species involved.  
  - Detailed threats and severity.  
  - Suggested conservation actions.  
- **Chats:** Notification sent to a configured Telegram chat based on credentials (`telegram.chatId`).  
- **Position:** `[1650, 220]`

### 8. Notify Authorities  
- **Type:** Telegram  
- **Function:** Similar to conservationists notification but directed to government or regulatory authorities.  
- **Message Includes:**  
  - Alert header and review request.  
  - Species involved and summary of threats.  
- **Chats:** Sent to a separate Telegram chat defined by `telegramAuthorities.chatId`.  
- **Position:** `[1650, 380]`  

### 9. Prepare Data for Storage  
- **Type:** Function  
- **Function:**  
  - Packages the AI analysis results with a timestamp for storage.  
  - Prepares a JSON object including current ISO timestamp and the analysis result.  
- **Position:** `[1150, 450]`  

### 10. Store Analysis Results  
- **Type:** Postgres (Database)  
- **Function:**  
  - Upserts the analysis data into the `urban_wildlife_monitoring` table in the database.  
  - Uses the timestamp as a unique constraint to handle conflicts (prevents duplicates for the same time).  
  - Stores serialized JSON of the AI analysis under `analysis` column.  
- **Database:** PostgreSQL using configured `PostgresDB` credentials.  
- **Position:** `[1400, 450]`  

---

## Credentials Required

- **IoT API Key:** Authorization token to fetch the latest sensor data.  
- **Telegram API for Conservationists:** Telegram bot API credentials and `chatId` for conservationists alerts.  
- **Telegram API for Authorities:** Separate Telegram bot API credentials and `chatId` for authority notifications.  
- **PostgresDB:** Credentials to connect to the PostgreSQL database for storing analysis results.

---

## How It Works (Step-by-Step)

1. **Workflow Trigger:** Automatically starts every day at midnight via the schedule trigger.  
2. **Data Collection:** Pulls the latest urban wildlife sensor data from an external IoT platform API.  
3. **Data Parsing:** Extracts wildlife presence signals and habitat condition metrics from raw IoT data for AI processing.  
4. **AI Analysis:** Runs advanced AI (OpenAI text-davinci-003) to interpret data, detect species, environmental threats, and recommend conservation actions.  
5. **Response Parsing:** Converts the AI-generated JSON string to a usable JSON object within the workflow.  
6. **Threat Detection:** Checks if threats are present in the AI analysis output.  
7. **Alert Dispatch:** If threats exist:  
   - Sends detailed alerts to conservationists.  
   - Sends summary alerts to authorities for further action.  
8. **Data Storage:** Saves the analyzed results with timestamp to a PostgreSQL database for historical reference and tracking.

---

## Database Schema Details

- **Table:** `urban_wildlife_monitoring`  
- **Columns:**  
  - `timestamp` (Primary Key): ISO-formatted timestamp of the analysis.  
  - `analysis`: JSON string containing AI analysis output (species, threats, severity, suggested actions).

---

## Summary

This workflow enables a continuous, automated, intelligent system for monitoring urban wildlife by combining IoT data and AI-driven analysis. It ensures timely alerts are sent to protect biodiversity and support conservation efforts efficiently.

---

## Notes

- Ensure all API keys and credentials are correctly stored and configured in n8n credentials.  
- Adjust the IoT platform URL and data parsing logic to fit the actual sensor data schema if different from the example.  
- Customize message formats and recipient chat IDs as per operational requirements.  
- PostgreSQL table and schema should already exist or be created prior to running the workflow.

---

**End of README**