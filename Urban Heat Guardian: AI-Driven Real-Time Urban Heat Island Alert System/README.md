# AI-Powered Real-Time Urban Heat Island Monitoring and Alert System

## Overview

This workflow provides a real-time monitoring and alert system for Urban Heat Island (UHI) effects using IoT sensor data, weather API data, AI-based predictions, and multi-channel notifications. It integrates temperature sensor data from IoT devices, fetches real-time local weather information, predicts heat island intensity using a machine learning model, determines alert levels with actionable recommendations, and sends alerts via Slack and Email.

---

## Workflow Components

### 1. **IoT Temperature Sensor (MQTT)**
- **Type:** MQTT Trigger
- **Purpose:** Listens to temperature data from IoT sensors.
- **Topic:** `iot/sensors/temperature`
- **Data Received:** JSON containing sensor temperature, location, and timestamp.

---

### 2. **Real-Time Weather API**
- **Type:** HTTP Request
- **Purpose:** Fetches current weather data for the city specified in the sensor data.
- **API Used:** OpenWeatherMap Current Weather Data API (`https://api.openweathermap.org/data/2.5/weather`)
- **Parameters:**
  - `q`: City name from sensor data (`{{$json["city"]}}`)
  - `appid`: API key from credentials
  - `units`: Metric system (°C)

---

### 3. **Merge and Prepare Data**
- **Type:** Function Node
- **Purpose:** Combines IoT sensor data and weather API response.
- **Outputs:** 
  - Sensor temperature, location, timestamp
  - Weather API temperature, humidity, wind speed, city

---

### 4. **Machine Learning Model - Predict Heat Island Intensity**
- **Type:** AI Node
- **Purpose:** Predicts the heat island intensity using merged data.
- **Model ID:** `urban-heat-island-model`
- **Input:** Merged sensor and weather data.
- **Output:** Heat island intensity prediction score.

---

### 5. **Determine Alerts and Recommendations**
- **Type:** Function Node
- **Purpose:** Interprets the AI prediction and sets alert levels and recommendations.
- **Logic:**
  - **High Alert (prediction > 7):**
    - Activate cooling centers
    - Issue heat advisories
    - Increase greenery and water features
    - Limit outdoor activity during peak heat hours
  - **Moderate Alert (4 < prediction ≤ 7):**
    - Encourage hydration
    - Monitor vulnerable groups
    - Reduce midday outdoor exposure
  - **Low Alert (prediction ≤ 4):**
    - Maintain regular monitoring
- **Outputs:** Alert level, recommendations, relevant sensor and location data, timestamp

---

### 6. **Send Slack Alert**
- **Type:** Slack Node
- **Purpose:** Sends formatted real-time alert messages to a Slack channel.
- **Channel:** `urban-heat-alerts`
- **Message Includes:**
  - Alert level
  - Location and city
  - Current sensor temperature
  - Predicted heat island intensity
  - Recommendations list
  - Timestamp

---

### 7. **Send Email Notification**
- **Type:** Email Send
- **Purpose:** Sends alert notifications via email to specified stakeholders.
- **Recipients:** `city.manager@example.com`, `residents@cityalerts.example.com`
- **Email Subject:** Urban Heat Island Alert with detected alert level
- **Email Body:**
  - Alert details including level, location, sensor temperature, prediction
  - Actionable recommendations
  - Timestamp

---

## Connections Flow

1. **IoT Temperature Sensor (MQTT)** triggers the workflow when new sensor data arrives.
2. Data is sent to **Real-Time Weather API** to fetch latest weather information.
3. Outputs from both data sources are merged in **Merge and Prepare Data**.
4. Prepared data is fed into the **Machine Learning Model** for heat island intensity prediction.
5. Predictions are analyzed in **Determine Alerts and Recommendations** to assign alert levels and guidance.
6. Alert information is simultaneously sent to:
   - **Slack Alert channel** for instant notifications.
   - **Email Notification** for formal alerts to managers and residents.

---

## Credentials Required

- **Weather API Credentials:**
  - API key for OpenWeatherMap
- **AI API Credentials:**
  - Access to machine learning model endpoint for prediction
- **Slack API Credentials:**
  - Bot token with access to the `urban-heat-alerts` channel
- **SMTP Credentials:**
  - Email server details for sending notifications

---

## Summary

This workflow empowers city managers and residents with timely and data-driven Urban Heat Island alerts, leveraging real-time sensor data, weather conditions, AI predictions, and multi-channel communication for effective heat risk mitigation.