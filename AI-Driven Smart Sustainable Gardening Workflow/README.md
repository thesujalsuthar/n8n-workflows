# AI-Powered Sustainable Gardening Companion

This workflow leverages IoT sensor data, weather information, AI-based pest detection, and automated messaging to provide personalized, sustainable gardening advice. It enables real-time monitoring and recommendations to promote healthy plant growth using data-driven insights.

---

## Workflow Overview

The workflow consists of the following key steps:

1. **Get IoT Sensor Data**  
   - Fetches real-time sensor data from IoT devices monitoring soil moisture, temperature, humidity, light intensity, and captures camera images for pest detection.

2. **Evaluate Soil Moisture**  
   - Analyzes soil moisture levels to determine if the plants need watering (soil moisture below 30% triggers watering need).

3. **Get Current Weather**  
   - Retrieves current weather data (temperature, humidity) for the relevant location to inform watering decisions.

4. **Watering Scheduler**  
   - Integrates soil moisture and weather data to generate a watering recommendation.  
   - Watering is advised if soil moisture is low, temperature is above 10°C, and humidity is below 70%.

5. **Pest Detection AI**  
   - Sends the latest camera image from the IoT system to an AI pest detection endpoint via HTTP POST request.  
   - Receives pest detection results specifying any pests found.

6. **Generate Gardening Tips**  
   - Combines pest detection results and watering recommendations to create personalized gardening advice including pest control suggestions, watering tips, soil health improvement, and crop rotation strategies.

7. **Send Tips to Slack**  
   - Sends the personalized gardening tips formatted as a message to a Slack channel (`#gardening-tips`) for easy access by gardeners.

---

## Node Descriptions

### 1. Get IoT Sensor Data (HTTP Request - GET)
- **Endpoint:** `iot/sensor-data`  
- **Data Retrieved:**  
  - `soilMoisture` (percentage)  
  - `temperature` (°C)  
  - `humidity` (percentage)  
  - `lightIntensity` (lux)  
  - `cameraImage` (image data for pest detection)  
- **Response Format:** JSON

### 2. Evaluate Soil Moisture (Function Node)
- Extracts soil moisture from sensor data.  
- Determines if watering is needed (`needsWatering` = true if soil moisture < 30%).

### 3. Get Current Weather (OpenWeather Node)
- Retrieves current weather data based on sensor location or defaults to "home".  
- Parameters:  
  - Units in metric  
  - Language English  

### 4. Watering Scheduler (Function Node)
- Inputs: Current weather data and soil moisture evaluation.  
- Logic:  
  - Suggests watering if soil moisture < 30%, temperature > 10°C, and humidity < 70%.  
  - Otherwise, recommends not watering.

### 5. Pest Detection AI (HTTP Request - POST)
- Sends the plant area image captured by sensors to an AI pest detection API: `https://api.example-ai.com/v1/pest-detection`.  
- Payload includes `imageData` from the sensor.  
- Receives detected pests and related info for decision making.

### 6. Generate Gardening Tips (Function Node)
- Combines pest detection results and watering advice to generate targeted gardening tips.  
- Tips include:  
  - Pest control recommendations (e.g., neem oil, beneficial insects).  
  - Watering best practices (timing and avoidance of overwatering).  
  - Soil health improvements (compost, mulching).  
  - Crop rotation and companion planting ideas.

### 7. Send Tips to Slack (Slack Node)
- Posts the compiled tips as a formatted message to the Slack channel `#gardening-tips`.  
- Message is prefixed with "🪴 AI-Powered Gardening Update:" for clarity.

---

## Data Flow & Connections

- **Get IoT Sensor Data** outputs to:  
  - Evaluate Soil Moisture  
  - Pest Detection AI  

- **Evaluate Soil Moisture** and **Get Current Weather** both feed into:  
  - Watering Scheduler  

- Outputs from **Pest Detection AI** and **Watering Scheduler** feed into:  
  - Generate Gardening Tips  

- Final personalized tips from **Generate Gardening Tips** are sent to Slack.

---

## Setup and Configuration

1. **IoT Sensor Endpoint**  
   - Ensure your IoT sensors expose data at the `iot/sensor-data` API path in JSON format.  
   - Include camera image data for pest detection in the JSON payload.

2. **Weather Data**  
   - Configure the OpenWeather node with a valid API key and region settings as applicable.

3. **AI Pest Detection API**  
   - Replace `https://api.example-ai.com/v1/pest-detection` with your actual AI pest detection endpoint.  
   - Ensure the endpoint accepts image data via POST with appropriate authentication if needed.

4. **Slack Integration**  
   - Configure Slack node with your workspace and bot token.  
   - Confirm the bot has permissions to post in the `#gardening-tips` channel.

---

## Notes

- This workflow supports sustainable gardening through automation and data-driven interventions.  
- Recommendations depend heavily on sensor accuracy and AI detection capabilities.  
- Customize thresholds and messages according to your garden’s specific requirements.

---

Harness this AI-powered system to monitor your garden effortlessly and maintain a thriving, healthy environment for your plants!