# AI-Powered Smart Urban Garden Care Assistant

This workflow leverages sensor data and AI analysis to provide intelligent plant care recommendations, manage watering schedules, and send pest alerts for a smart urban garden.

---

## Workflow Overview

The workflow consists of the following key steps:

1. **Get Environmental Data**  
   Fetches real-time environmental sensor data including temperature, humidity, soil moisture, and light levels from the garden's API.

2. **Get Plant Health Data**  
   Retrieves plant health metrics such as plant status and pest detection alerts.

3. **Combine Sensor Data**  
   Merges environmental data and plant health data into a single structured JSON object for analysis.

4. **AI Analyze Plant Conditions**  
   Uses the GPT-4 model to analyze combined sensor data and generate:  
   - Plant health assessment  
   - Watering schedule recommendations  
   - Pest detection alerts and advice  
   - Personalized gardening tips  

   The AI outputs a structured JSON object with these insights.

5. **Parse AI JSON Response**  
   Extracts and parses the JSON content from the AI's textual response for further use.

6. **Update Watering System**  
   Sends the recommended watering schedule to the garden's watering system API to automatically adjust irrigation.

7. **Send Pest and Tips Notification**  
   Sends a notification with pest alerts and gardening tips to the garden's notification system to inform the user.

---

## Nodes Detail

### 1. Get Environmental Data

- **Type:** HTTP Request (GET)  
- **URL:** `http://api.smartgarden.local/sensors/environment`  
- **Response:** JSON containing environmental metrics:
  - `temperature` (°C)
  - `humidity` (%)
  - `soilMoisture` (%)
  - `lightLevel` (lux)

### 2. Get Plant Health Data

- **Type:** HTTP Request (GET)  
- **URL:** `http://api.smartgarden.local/sensors/plantHealth`  
- **Response:** JSON data including:
  - `status` (general health assessment)
  - `pestsDetected` (array of detected pests)

### 3. Combine Sensor Data

- **Type:** Function  
- **Function:** Combines the JSON responses from environmental and plant health nodes into a single object:
  ```json
  {
    "temperature": ...,
    "humidity": ...,
    "soilMoisture": ...,
    "lightLevel": ...,
    "plantStatus": ...,
    "pestAlerts": ...
  }
  ```

### 4. AI Analyze Plant Conditions

- **Type:** OpenAI GPT-4  
- **Prompt:** Analyzes combined sensor data and requests JSON output with fields:  
  - `plantAssessment`  
  - `wateringSchedule`  
  - `pestAlerts`  
  - `gardeningTips`

- **Model Parameters:**  
  - Temperature: 0.7  
  - Max Tokens: 300  
  - Top_p: 1  
  - Frequency Penalty: 0  
  - Presence Penalty: 0  

### 5. Parse AI JSON Response

- **Type:** Function  
- **Function:** Parses the AI's text response to extract the JSON object for downstream nodes.

### 6. Update Watering System

- **Type:** HTTP Request (POST)  
- **URL:** `http://api.smartgarden.local/devices/wateringSystem/control`  
- **Request Body:**  
  ```json
  {
    "action": "schedule",
    "schedule": { watering schedule JSON from AI }
  }
  ```

- **Purpose:** Automatically configures the watering system based on AI recommendations.

### 7. Send Pest and Tips Notification

- **Type:** HTTP Request (POST)  
- **URL:** `http://api.smartgarden.local/devices/notificationSystem/send`  
- **Request Body:**  
  ```json
  {
    "title": "Garden Care Alert",
    "message": "Pest Alerts: [detected pests or 'No pests detected']. Gardening Tips: [personalized tips]"
  }
  ```

- **Purpose:** Notifies the user of pest alerts and provides customized gardening advice.

---

## Workflow Connections

- Environmental and plant health data nodes feed into the **Combine Sensor Data** node.
- Combined data is sent to the **AI Analyze Plant Conditions** node.
- AI response is parsed by the **Parse AI JSON Response** node.
- Parsed AI recommendations are used to update the watering system and send notifications simultaneously.

---

## Requirements

- Access to the smart garden's API at `http://api.smartgarden.local/`
- Valid OpenAI GPT-4 API key configured in the OpenAI node
- Functional devices connected for watering system control and notifications

---

## Summary

This workflow integrates live sensor data with AI-driven analysis to optimize plant care operations in an urban garden. It automates watering schedules and keeps users informed about garden health and pest issues, enhancing plant growth and maintenance efficiency.