# AI-Driven Sustainable Gardening Advisor and Smart Irrigation Scheduler

This workflow leverages AI and sensor data to provide personalized daily gardening advice and an optimized irrigation schedule based on current soil moisture and weather forecasts. It automates data collection, advice generation, and communication via email, helping gardeners maintain a sustainable and healthy garden.

---

## Workflow Overview

The workflow runs daily at 5 AM and performs the following steps:

1. **Get Weather Forecast:**  
   Fetches a 7-day weather forecast for the specified location using OpenWeather API.

2. **Get Soil Moisture Data:**  
   Retrieves current soil moisture data from a connected soil moisture sensor via its API.

3. **Merge Data:**  
   Combines weather forecast and soil moisture data into a single JSON object for processing.

4. **Generate Gardening Tips:**  
   Uses heuristic logic to create tailored gardening tips and disease prevention advice based on plant types, soil moisture, and weather conditions.

5. **Create Irrigation Schedule:**  
   Generates a smart irrigation schedule considering current soil moisture levels and upcoming rain forecasts to optimize watering efficiency.

6. **Send Gardening Advice Email:**  
   Sends a daily email to the user containing personalized gardening tips, disease prevention advice, and the irrigation schedule.

---

## Nodes Description

### 1. Trigger Daily at 5AM  
- **Type:** Schedule Trigger  
- **Purpose:** Triggers the workflow every day at 5:00 AM to initiate data retrieval and advice generation.

### 2. Get Weather Forecast  
- **Type:** Weather  
- **Operation:** Retrieves the 7-day forecast for a specified location.  
- **Credentials:** OpenWeather API key required.

### 3. Get Soil Moisture Data  
- **Type:** Generic API  
- **Operation:** Obtains soil moisture readings from the sensor API identified as `soilMoistureSensor1`.  
- **Credentials:** API access for soil moisture sensor required.

### 4. Merge Data  
- **Type:** Merge  
- **Purpose:** Merges weather forecast and soil moisture data into a single JSON object for subsequent processing.

### 5. Generate Gardening Tips  
- **Type:** Function  
- **Logic:**  
  - Checks soil moisture for each plant type to advise watering actions.  
  - Alerts if rain is likely to reduce irrigation needs.  
  - Provides disease prevention advice based on humidity and precipitation data.

### 6. Create Irrigation Schedule  
- **Type:** Function  
- **Logic:**  
  - Sets irrigation timing and duration based on soil moisture thresholds.  
  - Skips irrigation if sufficient moisture is present or rain is expected.  

### 7. Send Gardening Advice Email  
- **Type:** Email Send  
- **Content:** Sends a compiled email including:  
  - Personalized gardening tips  
  - Disease prevention advice  
  - Recommended irrigation schedule  
- **Credentials:** SMTP credentials for sending emails required.

---

## Input and Output Details

- **Input Parameters:**  
  - `location`: Geographic location for weather forecast  
  - `plantTypes`: Array of plant species in the garden  
  - `userEmail`: Email address to send daily advice  
  - Soil moisture and weather data are automatically fetched from APIs.

- **Email Content Template:**

```
Hello,

Here are your personalized gardening tips for today:

- [Tip 1]
- [Tip 2]
...

Disease prevention advice:
- [Advice 1]
- [Advice 2]
...

Irrigation Schedule:
* [Time and duration or skip note]

Happy Gardening! 🌱
```

---

## Credentials Required

- **OpenWeather_API:** API key for fetching weather data.  
- **SoilMoistureSensor_API:** API access for soil moisture sensor data.  
- **AI_API_Credentials:** Basic HTTP authentication for the AI service analyzing plant data.  
- **SMTP_Email_Credentials:** SMTP server credentials for email sending.

---

## Customization

- Update the `location`, `plantTypes`, and `userEmail` fields within the workflow or at the trigger event input to personalize the advice.  
- Adjust soil moisture sensor ID if using actual different sensor hardware.  
- Modify function node logic to fine-tune advice and irrigation based on specific plant needs or climate conditions.

---

## Activation

- The workflow is disabled by default (`active: false`).  
- Enable the workflow in your n8n instance to start daily automated gardening advice delivery.

---

## Summary

This end-to-end automated solution helps gardeners sustainably manage their plants by integrating real-time sensor data, weather forecasting, AI analysis, and smart communication — all orchestrated seamlessly with n8n.