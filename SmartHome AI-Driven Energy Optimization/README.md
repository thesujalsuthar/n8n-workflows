# SmartHome Energy Optimization Workflow

This workflow is designed to optimize home energy consumption by integrating data from smart meters and IoT sensors, analyzing it with AI, sending actionable recommendations, and automatically adjusting smart devices to improve efficiency while maintaining comfort. The process runs every 5 minutes.

---

## Workflow Overview

1. **Trigger (Every 5 Minutes)**
   - Initiates the entire workflow at 5-minute intervals.

2. **Fetch Energy Consumption Data**
   - Sends a GET request to the Smart Meter Provider API to retrieve current energy consumption metrics.
   - Authentication via API key (`smartMeterApi` credentials).
   - Endpoint: `https://api.smartmeterprovider.com/v1/energy-consumption`
   - Response expected in JSON format.

3. **Fetch IoT Sensor Data**
   - Sends a GET request to the Smart Home Platform API to fetch real-time IoT sensor data (voltage, temperature, humidity).
   - Authentication via API key (`smartHomePlatformApi` credentials).
   - Endpoint: `https://api.smarthomeplatform.com/v1/iot-sensors`
   - Response expected in JSON format.

4. **Combine and Aggregate Data**
   - Merges energy consumption data and IoT sensor readings into a single structured JSON object.
   - Adds a timestamp marking the aggregation time.
   - Sample combined JSON structure:
     ```json
     {
       "timestamp": "2024-06-XXTXX:XX:XX.XXXZ",
       "energyUsage": <value>,
       "voltage": <value>,
       "temperature": <value>,
       "humidity": <value>
     }
     ```

5. **AI Optimization Analysis**
   - Sends the combined data to OpenAI GPT-4 for analysis.
   - Prompt requests identification of energy optimization opportunities to reduce consumption and carbon footprint while maintaining comfort.
   - AI generates specific recommendations and potential energy savings.
   - Parameters:
     - Model: GPT-4
     - Max Tokens: 300
     - Temperature: 0.7

6. **Send Notification**
   - Delivers AI-generated energy optimization recommendations as a push notification.
   - Uses Pushbullet service.
   - Notification includes:
     - Title: "SmartHome Energy AI Optimizer Alert"
     - Message: Detailed recommendations from AI.
     - Priority: High
     - Target user ID from `notificationService` credentials.

7. **Adjust Thermostat**
   - Sends a POST request to the Smart Home Platform API to adjust the thermostat.
   - Uses AI recommendations or defaults to 22°C if none provided.
   - Endpoint: `https://api.smarthomeplatform.com/v1/devices/actions`
   - Parameters:
     - `deviceId`: "thermostat_01"
     - `action`: "adjust_temperature"
     - `value`: recommended temperature or 22

8. **Adjust Lighting**
   - Sends a POST request to the Smart Home Platform API to adjust lighting brightness.
   - Uses AI recommendations or defaults to 70% brightness if none provided.
   - Endpoint: `https://api.smarthomeplatform.com/v1/devices/actions`
   - Parameters:
     - `deviceId`: "lighting_01"
     - `action`: "set_brightness"
     - `value`: recommended brightness or 70

---

## Credentials Required

- **Smart Meter API (`smartMeterApi`)**
  - API Key for authentication with the Smart Meter Provider.

- **Smart Home Platform API (`smartHomePlatformApi`)**
  - API Key for authentication with the Smart Home Platform to fetch sensor data and control smart devices.

- **Notification Service (`notificationService`)**
  - Authentication details for Pushbullet or equivalent push notification service.
  - User ID for targeting notifications.

---

## Execution Flow

- The **cron trigger** initiates parallel HTTP GET requests to:
  - Fetch energy consumption data.
  - Fetch IoT sensor data.
- Both data responses are merged in the "Combine and Aggregate Data" function.
- The combined data is sent for AI analysis.
- AI's output triggers three parallel actions:
  - Sending a notification with recommendations.
  - Adjusting the thermostat settings.
  - Adjusting the lighting brightness.

---

## Summary

This automated workflow ensures continuous, intelligent monitoring and optimization of home energy usage by combining real-time data ingestion, AI-driven analysis, user notification, and automated adjustments to key smart home devices.

---

## Notes

- All HTTP requests include `Authorization` headers using bearer tokens retrieved securely from credentials.
- The workflow is designed for extensibility with customizable device IDs and default adjustment values.
- The AI model prompt can be refined further to tailor energy optimization goals.