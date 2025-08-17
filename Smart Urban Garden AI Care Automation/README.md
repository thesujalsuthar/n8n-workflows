# AI-Powered Smart Urban Garden Assistant

This workflow automates plant care in a smart urban garden by combining real-time environmental sensor data with weather forecasts, using AI to generate optimized watering schedules and plant care recommendations. The results are then sent as commands to irrigation systems and notifications to users.

---

## Workflow Overview

The workflow consists of several connected nodes executing the following steps:

### 1. Fetch Sensor Data  
- **Node:** `Fetch Sensor Data`  
- **Type:** HTTP Request  
- **Description:** Retrieves real-time environmental data from local garden sensors including light, temperature, and humidity in JSON format.  
- **Endpoint:** `http://environmental-sensor.local/api/data`

### 2. Get Weather Forecast  
- **Node:** `Get Weather Forecast`  
- **Type:** OpenWeatherMap API  
- **Description:** Fetches weather forecast data for the configured city in metric units to anticipate external environmental changes affecting plant care.  
- **Parameters:**  
  - City: Replace `"YOUR_CITY_NAME"` with your actual city.  
  - Units: Metric system (ºC, mm, etc.)

### 3. Combine Data  
- **Node:** `Combine Data`  
- **Type:** Function  
- **Description:** Merges sensor readings with weather forecast data into a structured JSON object containing:  
  - Light intensity  
  - Temperature  
  - Humidity  
  - Weather forecast of the day

### 4. AI Plant Care Analysis  
- **Node:** `AI Plant Care Analysis`  
- **Type:** HTTP Request to OpenAI GPT-4 API  
- **Description:** Sends combined environmental data to the OpenAI GPT-4 model for analysis. The AI assistant specializes in urban gardening and generates:  
  - Automated watering schedules  
  - Plant care recommendations optimized for plant health and water efficiency  
- **OpenAI Settings:**  
  - Model: GPT-4  
  - Temperature: 0.5 for balanced creativity and accuracy  
  - System prompt defines gardening expertise  
- **Authentication:** Uses JWT with OpenAI API key  

### 5. Send Watering Commands  
- **Node:** `Send Watering Commands`  
- **Type:** MQTT Publish  
- **Description:** Publishes the AI-generated watering schedule and related commands to the MQTT topic `smartgarden/watering` to automate irrigation equipment.  
- **MQTT Broker:** Configure with your MQTT broker credentials.

### 6. Send Plant Care Recommendations  
- **Node:** `Send Plant Care Recommendations`  
- **Type:** Telegram  
- **Description:** Sends the plant care recommendations as a message to a Telegram chat via a bot for user notification and manual oversight.  
- **Parameters:**  
  - Chat ID: Replace `"YOUR_CHAT_ID"` with the target Telegram chat ID.  
- **Authentication:** Telegram Bot API credentials required.

---

## Setup Instructions

1. **Sensor API**  
   Ensure your environmental sensor platform exposes data at the provided endpoint. Update the URL in the `Fetch Sensor Data` node if different.

2. **OpenWeatherMap Node**  
   Replace `"YOUR_CITY_NAME"` with your city to receive accurate forecasts. Set up OpenWeatherMap API key if required.

3. **OpenAI API Credentials**  
   Add your OpenAI API key to the credentials named `OpenAI API`.

4. **MQTT Broker Configuration**  
   Set your MQTT broker connection details in the credentials for the `Send Watering Commands` node.

5. **Telegram Bot Setup**  
   Create a Telegram bot and obtain the Bot API token. Configure credentials in `Send Plant Care Recommendations` node and specify the Telegram chat ID.

---

## Workflow Execution Flow

```
Fetch Sensor Data → Get Weather Forecast → Combine Data → AI Plant Care Analysis → 
  ├─ Send Watering Commands (via MQTT)
  └─ Send Plant Care Recommendations (via Telegram)
```

---

## Notes

- This workflow runs within an n8n automation environment.
- Replace placeholders such as city name, MQTT broker info, Telegram chat ID, and API keys with your actual data before activating.
- The AI-generated recommendations are tailored for sustainable urban gardening focusing on resource efficiency.

---

## License

This workflow and its components can be used and modified freely with attribution.