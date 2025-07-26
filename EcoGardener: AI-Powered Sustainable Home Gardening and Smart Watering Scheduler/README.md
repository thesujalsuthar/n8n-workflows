# AI-Driven Sustainable Home Gardening Advisor and Smart Watering Scheduler

This workflow leverages real-time weather data, soil moisture sensor readings, and AI-powered analysis to provide personalized, eco-friendly gardening advice along with an optimized watering schedule. It helps home gardeners conserve water, promote plant health, and maintain sustainable gardening practices.

---

## Workflow Overview

The workflow consists of the following key steps:

1. **Fetch Current Weather**  
   Retrieves the current weather data for the user's specified location using the OpenWeatherMap API.

2. **Get Soil Moisture Data**  
   Collects soil moisture readings from a connected sensor API, which provides the current moisture level as a percentage.

3. **Assess Watering Needs**  
   A custom function node analyzes weather conditions and soil moisture to decide whether watering is required. It uses these criteria:  
   - Soil moisture level below a predefined threshold (default 40%)  
   - No precipitation (e.g., rain) expected currently

4. **Generate AI Gardening Advice & Schedule**  
   An OpenAI GPT-4 model generates personalized sustainable gardening advice and a smart watering schedule based on:  
   - User’s plant and garden data input  
   - Current weather conditions  
   - Soil moisture status  
   
   The advice focuses on water conservation, sustainable plant care, and optimized watering timing, returned as a JSON object.

5. **Notify User**  
   Sends the generated gardening advice and watering schedule to the user via a Telegram channel.

---

## Detailed Node Descriptions

### 1. Fetch Current Weather  
- **Node Type:** OpenWeatherMap  
- **Parameters:**  
  - Location: Dynamic input from the user's location JSON field (`userLocation`)  
- **Purpose:** Acquire up-to-date weather data including temperature, precipitation status, and other environmental information to inform watering decisions.

### 2. Get Soil Moisture Data  
- **Node Type:** HTTP Request  
- **Parameters:**  
  - Device ID: Identifier for the soil moisture sensor API (`soil-moisture-sensor-001`)  
- **Purpose:** Retrieve current soil moisture percentage from the sensor API. It assumes the API response contains a `moisture` field with the moisture level.

### 3. Assess Watering Needs  
- **Node Type:** Function  
- **Function Logic:**  
  - Extracts weather data and soil moisture percentage.  
  - Defines a moisture threshold of 40% to determine the need for watering.  
  - Checks if currently raining via weather condition keywords.  
  - Returns whether watering is needed, soil moisture level, precipitation status, and temperature in Celsius.

### 4. Generate AI Gardening Advice & Schedule  
- **Node Type:** OpenAI (GPT-4)  
- **Parameters:**  
  - Prompt: Instructs the AI to act as a sustainable gardening advisor generating eco-friendly care tips and a watering schedule tailored to provided inputs.  
  - Temperature: 0.7 (balanced creativity and coherence)  
  - Max Tokens: 500  
- **Input:** JSON containing plant and garden info, weather, and soil moisture data.  
- **Output:** JSON with personalized advice and watering schedule emphasizing sustainability and water conservation.

### 5. Notify User  
- **Node Type:** Telegram  
- **Parameters:**  
  - Target Channel: `garden-watering`  
  - Message: Combines AI-generated advice and watering schedule into a single notification message.  
- **Purpose:** Deliver timely, actionable gardening recommendations directly to the user’s Telegram channel.

---

## Setup Instructions

1. **Configure API Credentials:**  
   - Set your OpenWeatherMap API key in the `Fetch Current Weather` node configuration.  
   - Provide the soil moisture sensor API endpoint and ensure it returns moisture data in expected format.  
   - Add your OpenAI API key in the `Generate AI Gardening Advice & Schedule` node credentials.  
   - Set your Telegram Bot API key in the `Notify User` node credentials and configure the target channel.

2. **Define User Input:**  
   Provide the user's location in JSON format (under `userLocation`) and send plant/garden data accordingly for personalized advice generation.

3. **Deploy and Activate Workflow:**  
   After populating credentials and inputs, activate the workflow to start automated real-time gardening advisories.

---

## Notes

- Moisture threshold (40%) can be adjusted within the `Assess Watering Needs` function node to suit different plant types or soil conditions.
- The AI prompt can be customized to refine the tone, detail, or focus of the gardening advice.
- Ensure the soil moisture sensor API is reliable and responses match the expected format for accurate assessments.

---

## Summary

This workflow intelligently combines environmental sensing, IoT data, and advanced AI to empower sustainable home gardening. By delivering timely, data-driven advice and smart watering schedules, it helps gardeners optimize water usage while maintaining healthy plants and ecosystems.