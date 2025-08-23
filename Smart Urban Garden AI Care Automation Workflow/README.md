# Smart Urban Garden AI Care Automation

This workflow automates the monitoring and care of an urban garden by integrating sensor data, weather forecasts, predictive analytics, and automated actions to optimize plant care.

---

## Overview

The workflow collects data from multiple sensors (soil moisture, sunlight, and temperature) via ThingSpeak API, fetches the local weather forecast from OpenWeatherMap, analyzes the combined data to determine watering needs and care tips, logs sensor data in Google Sheets, sends care update emails, and optionally activates an automated watering system.

---

## Workflow Components

### 1. Get Soil Moisture Sensor Data
- **Node Type:** HTTP Request
- **Description:** Fetches the latest soil moisture reading from ThingSpeak.
- **Request:**  
  `GET https://api.thingspeak.com/channels/CHANNEL_ID/fields/1.json?results=1`  
- **Authentication:** API key header (`api-key: YOUR_THINGSPEAK_API_KEY`)

---

### 2. Get Sunlight Sensor Data
- **Node Type:** HTTP Request
- **Description:** Retrieves the latest sunlight measurement (in hours) from ThingSpeak.
- **Request:**  
  `GET https://api.thingspeak.com/channels/CHANNEL_ID/fields/2.json?results=1`  
- **Authentication:** API key header

---

### 3. Get Temperature Sensor Data
- **Node Type:** HTTP Request
- **Description:** Acquires the latest temperature reading from ThingSpeak.
- **Request:**  
  `GET https://api.thingspeak.com/channels/CHANNEL_ID/fields/3.json?results=1`  
- **Authentication:** API key header

---

### 4. Get Weather Forecast
- **Node Type:** HTTP Request
- **Description:** Requests real-time weather data from OpenWeatherMap for specified latitude and longitude.
- **Request:**  
  `GET https://api.openweathermap.org/data/2.5/weather?lat=YOUR_LATITUDE&lon=YOUR_LONGITUDE&appid=YOUR_OPENWEATHER_API_KEY&units=metric`
- **Parameters:**  
  - `lat`: Geographic latitude of the garden  
  - `lon`: Geographic longitude of the garden  
  - `appid`: OpenWeatherMap API key  
  - `units`: Metric system (`metric`)

---

### 5. Analyze Sensor Data and Weather
- **Node Type:** Function
- **Description:** Analyzes sensor readings and weather data to:  
  - Evaluate if watering is needed based on soil moisture and rain forecast.  
  - Generate care tips regarding sunlight exposure and temperature stress.  
- **Logic Highlight:**  
  - Watering needed if soil moisture below 30 and no rain forecast.  
  - Care tips given if sunlight < 6 hours or temperature outside 15-30°C range.  

---

### 6. Predictive Analytics
- **Node Type:** Function
- **Description:** Performs basic prediction for next-day watering need based on current soil moisture and temperature forecast.
- **Logic:**  
  Predicts watering will be needed if soil moisture < 40% and forecast temperature > 15°C.

---

### 7. Check Watering Need
- **Node Type:** Function
- **Description:** Filters the workflow path to proceed with watering activation only if watering is needed.

---

### 8. Activate Watering System (Optional)
- **Node Type:** HTTP Request
- **Description:** Sends a command to an automated watering device API to water the garden for a specified duration (30 seconds).
- **Request:**  
  `POST https://api.example-watering-system.com/devices/DEVICE_ID/water`  
- **Headers:**  
  - `Authorization: Bearer YOUR_WATERING_SYSTEM_API_TOKEN`  
- **Body:**  
  ```json
  {
    "duration": "30"
  }
  ```
- **Note:** This node is disabled by default and should be enabled only when the watering system and API are configured properly.

---

### 9. Log Sensor Data
- **Node Type:** Google Sheets
- **Description:** Appends current sensor data and watering decision to a Google Sheets document for record-keeping.
- **Sheet Details:**  
  - `sheetId`: Your Google Sheet ID  
  - `range`: SensorLogs!A1:E1  
- **Logged Data:** Timestamp, Soil Moisture, Sunlight, Temperature, Watering Needed (boolean)

---

### 10. Send Care Tips Email
- **Node Type:** Gmail
- **Description:** Sends an email containing the latest sensor readings, watering recommendation, and care tips.
- **Email Details:**  
  - Recipient: `user@example.com` (update to your email)  
  - Subject: "Smart Urban Garden Care Update"  
  - Email Body Template:  
    ```
    Soil Moisture: {{soilMoisture}}%
    Sunlight: {{sunlight}} hours
    Temperature: {{temperature}} °C

    Watering Recommendation: {{wateringRecommendation}}

    Care Tips:
    {{careTips}}
    ```

---

## Setup Instructions

1. **ThingSpeak Setup:**  
   - Replace `CHANNEL_ID` with your ThingSpeak channel ID.  
   - Replace `YOUR_THINGSPEAK_API_KEY` with your ThingSpeak read API key.

2. **OpenWeatherMap Setup:**  
   - Replace `YOUR_LATITUDE` and `YOUR_LONGITUDE` with your garden’s coordinates.  
   - Replace `YOUR_OPENWEATHER_API_KEY` with your OpenWeatherMap API key.

3. **Watering System API:**  
   - Replace API URL and `DEVICE_ID` with your watering device endpoint and device ID.  
   - Replace `YOUR_WATERING_SYSTEM_API_TOKEN` with your authorization token.  
   - Enable the "Activate Watering System" node after setup.

4. **Google Sheets:**  
   - Replace `YOUR_GOOGLE_SHEET_ID` with the ID of your Google Sheet used for logging.  
   - Configure Google Sheets credentials in n8n for authentication.

5. **Email:**  
   - Set the recipient email in the "Send Care Tips Email" node.  
   - Authenticate Gmail node for sending emails.

---

## Notes

- Disable or enable the watering system node depending on your real hardware availability.
- Adjust thresholds for soil moisture, sunlight, and temperature in the analysis function as needed for your specific plants.
- This workflow runs best when scheduled periodically (e.g., hourly or daily) to regularly monitor and act upon garden conditions.

---

## License

This workflow is provided as-is, free to use and customize for personal or commercial smart gardening projects.