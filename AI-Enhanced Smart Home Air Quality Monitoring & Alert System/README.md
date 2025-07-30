# AI-Powered Home Air Quality Monitor and Alert System

This workflow is designed to continuously monitor indoor air quality using sensor data, analyze it using AI, and trigger alerts and corrective actions when harmful conditions are detected.

---

## Workflow Overview

1. **MQTT Trigger - Air Quality Sensor**  
   Listens to the MQTT topic `home/air-quality` for incoming sensor data with QoS level 1.

2. **Parse Sensor Data**  
   Extracts and parses the raw JSON payload from the MQTT message to retrieve key air quality metrics:
   - Temperature
   - Humidity
   - PM2.5 concentration
   - CO2 levels
   - Volatile Organic Compounds (VOC) levels

3. **AI Analysis - Air Quality Check**  
   Sends the parsed sensor data to an AI model (`gpt-3.5-turbo`) with a system prompt acting as an air quality analyst. The AI evaluates the data to detect pollutants or harmful indoor air conditions and returns a JSON object containing:
   - `alert`: Boolean indicating if a problem is detected.
   - `message`: Explanation of the detected condition.
   - `recommendation`: Suggested action to improve air quality.

4. **Parse AI Response**  
   Parses the AI-generated JSON output to retrieve the alert status, message, and recommendations.

5. **Check Alert**  
   A conditional node that routes the workflow based on whether the `alert` is `true` or `false`.

6. If an alert is detected (`true`):
   - **Send Email Notification**  
     Sends an email to `user@example.com` with the alert message and recommendation.
   
   - **Activate Air Purifier**  
     Sends a command to the device with ID `air_purifier_01` to turn on the air purifier to mitigate poor air quality.

---

## Node Details

### MQTT Trigger - Air Quality Sensor
- **Type:** MQTT Trigger  
- **Topic:** `home/air-quality`  
- **QoS:** 1  
- **Description:** Subscribes to real-time updates from the air quality sensor network.

### Parse Sensor Data
- **Type:** Function  
- **Code:** Parses MQTT JSON payload extracting:
  - `temperature`
  - `humidity`
  - `pm2_5`
  - `co2`
  - `voc`

### AI Analysis - Air Quality Check
- **Type:** OpenAI Chat Completion  
- **Model:** `gpt-3.5-turbo`  
- **Prompt:**  
  System Role: Acts as an air quality analyst  
  User Input: Current sensor readings  
- **Output:** JSON with keys `alert`, `message`, and `recommendation`.

### Parse AI Response
- **Type:** Function  
- **Code:** Parses AI output from stringified JSON into a structured format for further use.

### Check Alert
- **Type:** If  
- **Condition:** Evaluates if `alert === true`.

### Send Email Notification
- **Type:** Email Send  
- **Recipient:** `user@example.com`  
- **Subject:** "Home Air Quality Alert"  
- **Body:** Contains alert message and recommendation from AI analysis.

### Activate Air Purifier
- **Type:** HTTP Request  
- **Operation:** Sends command to device `air_purifier_01`  
- **Command:** `"turn_on"`  
- **Description:** Automatically activates air purifier hardware when poor air quality is detected.

---

## How It Works

- The workflow waits for air quality data through MQTT messages.
- Raw sensor data is parsed and fed into an AI model for detailed analysis.
- Based on AI's assessment, if unsafe conditions are detected, the system triggers two parallel actions:
  1. Email alert to notify the home user.
  2. Activation of the air purifier to improve indoor air quality.
  
This solution leverages AI to provide contextual understanding of raw sensor readings and automates alerts and mitigation for healthier indoor environments.

---

## Prerequisites

- MQTT broker with sensors publishing to `home/air-quality`
- An OpenAI API key with access to GPT-3.5-turbo
- Configured email sending credentials in n8n
- HTTP endpoint or API capable of receiving device commands for air purifier control

---

## Configuration

1. **MQTT Trigger**  
   Update MQTT broker credentials and topic if needed.

2. **OpenAI Node**  
   Ensure your API credentials are set in n8n credentials.

3. **Email Node**  
   Replace `user@example.com` with your notification email address.

4. **Air Purifier Control**  
   Set the correct device ID and confirm API endpoint/access for issuing commands.

---

## Summary

This AI-powered workflow simplifies real-time home air quality monitoring by integrating sensor data ingestion, AI-driven analysis, smart alerts, and automated device control, ensuring a safer and healthier living environment.