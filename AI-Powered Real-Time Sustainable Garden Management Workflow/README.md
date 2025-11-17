# AI-Driven Smart Sustainable Gardening Workflow

This workflow leverages real-time sensor data and AI technology to optimize plant care through personalized watering, fertilizing schedules, and alerts for plant health or environmental issues.

---

## Workflow Overview

1. **Receive Sensor Data**  
   Triggered via HTTP GET on the `/sensor-data` endpoint, this node receives JSON payloads containing live sensor readings related to soil moisture, temperature, and light level.

2. **Parse Sensor Data**  
   Extracts relevant parameters (`soilMoisture`, `temperature`, `lightLevel`) from the incoming sensor data to prepare it for AI analysis.

3. **AI Plant Care Recommendations**  
   Sends the extracted sensor data to the OpenAI GPT-4 model using a chat completion API call, configured as an expert horticulturist AI. The AI provides tailored recommendations for watering, fertilizing, along with any relevant warnings about plant health or environmental conditions.

4. **Extract Tasks from AI Response**  
   Parses the AI-generated text to identify specific instructions related to watering, fertilizing, and alerts. Regular expressions are used to extract these actionable tasks and messages distinctly.

5. **Schedule Watering Task**  
   If the AI suggests watering, this node prepares the watering task. It filters out "no watering needed" instructions, generating a structured task for scheduling.

6. **Schedule Fertilizing Task**  
   Similar to watering, this node sets up fertilizing tasks based on AI recommendations unless it explicitly states that no fertilizing is needed.

7. **Prepare Alerts**  
   Collects any alerts or warnings identified in the AI response. Alerts relate to plant health or environmental risks that require attention.

8. **Send Alert Email**  
   Sends an email notification with alert details to a specified recipient to ensure timely awareness and intervention when needed.

9. **Create Watering Schedule**  
   Submits a POST request to `/garden-tasks/schedule-water` with task details to schedule automated or manual watering activities on a daily cadence.

10. **Create Fertilizing Schedule**  
    Sends a POST request to `/garden-tasks/schedule-fertilize` with fertilizing task details to schedule fertilizer application on a weekly basis.

---

## Detailed Node Descriptions

### 1. Receive Sensor Data  
- **Type:** HTTP Trigger  
- **Method:** GET  
- **Endpoint:** `/sensor-data`  
- **Response:** Returns all received entries immediately.  
- **Purpose:** Entry point to receive live environmental data.

### 2. Parse Sensor Data  
- **Type:** Function  
- **Function:** Extracts the following from the input JSON:  
  - `soilMoisture`  
  - `temperature`  
  - `lightLevel`

### 3. AI Plant Care Recommendations  
- **Type:** HTTP Request  
- **API:** OpenAI Chat Completion (GPT-4)  
- **Authentication:** Bearer Token via stored OpenAI API Key  
- **Prompt:**  
  - System: Expert horticulturist AI specializing in sustainable gardening  
  - User: Inputs sensor readings, requests personalized recommendations  
- **Response:** AI-generated text with care instructions and alerts

### 4. Extract Tasks from AI Response  
- **Type:** Function  
- **Function:**  
  - Extracts watering, fertilizing instructions using regex  
  - Gathers any warnings or alerts into a consolidated message

### 5. Schedule Watering Task  
- **Type:** Function  
- **Condition:** Executes only if watering instruction exists and differs from "no watering needed"  
- **Output:** Structured watering task with specific instructions

### 6. Schedule Fertilizing Task  
- **Type:** Function  
- **Condition:** Executes only if fertilizing instruction exists and differs from "no fertilizing needed"  
- **Output:** Structured fertilizing task with specific instructions

### 7. Prepare Alerts  
- **Type:** Function  
- **Function:** Wraps any alerts into a formatted alert message for notification

### 8. Send Alert Email  
- **Type:** Email Send  
- **From:** `gardening-system@yourdomain.com`  
- **To:** `user@example.com`  
- **Subject:** "Plant Health or Environment Alert"  
- **Content:** Alerts formatted in plain text and HTML  
- **Credentials:** Uses configured SMTP credentials  

### 9. Create Watering Schedule  
- **Type:** HTTP Request  
- **Method:** POST  
- **Endpoint:** `/garden-tasks/schedule-water`  
- **Body Parameters:**  
  - `task`: "water plants"  
  - `instruction`: Detailed watering instructions from AI response  
  - `schedule`: "daily"

### 10. Create Fertilizing Schedule  
- **Type:** HTTP Request  
- **Method:** POST  
- **Endpoint:** `/garden-tasks/schedule-fertilize`  
- **Body Parameters:**  
  - `task`: "fertilize plants"  
  - `instruction`: Detailed fertilizing instructions from AI response  
  - `schedule`: "weekly"

---

## Credentials & Configuration

- **OpenAI API Key:** Required for accessing GPT-4 API in "AI Plant Care Recommendations" node. Stored securely in n8n credentials under the name `OpenAI API`.
- **SMTP Credentials:** Required for sending alert emails, configured in n8n under `SMTP Email`.

---

## How to Use

1. Deploy the workflow to your n8n instance.
2. Configure the OpenAI API credential with your API key.
3. Set up SMTP email credentials.
4. Point your sensor system or test client to send GET requests with sensor data to the `/sensor-data` endpoint.
5. The workflow processes sensor data, queries AI for plant care recommendations, schedules gardening tasks, and sends alerts if necessary.

---

## Notes

- The workflow assumes external endpoints `/garden-tasks/schedule-water` and `/garden-tasks/schedule-fertilize` exist and accept POST requests to schedule corresponding tasks.
- Email recipients and sender addresses should be updated to reflect actual users.
- Sensor data JSON must include keys: `soilMoisture`, `temperature`, and `lightLevel` for accurate AI recommendations.
- AI temperature setting is 0.7 for balanced creativity and coherence in responses.

---

By automating plant care with AI and sensor technology, this workflow supports sustainable gardening practices tailored to real-time garden conditions.