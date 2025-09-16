# AI-Powered Smart Home Indoor Gardening Assistant

This workflow is designed to provide a comprehensive AI-driven solution for indoor gardening, leveraging IoT sensors, AI-powered plant disease detection, and personalized care recommendations. It automates monitoring, care advice generation, task scheduling, and alerts to help maintain optimal plant health within a smart home environment.

---

## Workflow Overview

The workflow consists of two main functional streams:

1. **Real-Time Environmental Monitoring & Care Recommendations**
2. **AI-Driven Plant Disease Detection & Treatment Suggestions**

---

## Nodes Description

### IoT Sensor Inputs (Data Acquisition)

- **Soil Moisture Sensor**
  - Device: `soilMoistureSensor`
  - Measures soil moisture every 300 seconds (5 minutes)
  - Property: `moisture`
  
- **Light Level Sensor**
  - Device: `lightLevelSensor`
  - Measures light intensity every 300 seconds (5 minutes)
  - Property: `light`
  
- **Temperature Sensor**
  - Device: `temperatureSensor`
  - Measures ambient temperature every 300 seconds (5 minutes)
  - Property: `temperature`

These sensors continuously monitor key environmental conditions affecting indoor plant health.

---

### Aggregate Sensor Data

- **Aggregate Sensor Data**
  - A function node that consolidates data from the three sensors into a single JSON object with properties: `soilMoisture`, `lightLevel`, and `temperature`.
  - This step prepares the collected sensor data for AI analysis.

---

### AI-Powered Plant Care Advice

- **Generate Care Recommendations**
  - Utilizes OpenAI GPT-4 with a conversational prompt.
  - System role: Provides optimal indoor plant care advice based on sensor readings.
  - User message: Inputs current sensor data on soil moisture, light level, and temperature.
  - Output: Specific recommendations for watering, lighting, and temperature adjustments.

- **Create Watering Schedule and Alerts**
  - Another GPT-4 node that:
    - Parses the care advice.
    - Generates an optimized watering schedule in cron format.
    - Determines if user alerts are necessary.
  
- **Trigger Watering Workflow**
  - Executes a separate "Watering Workflow" for automated watering based on the schedule generated.

- **Send Care Alert**
  - Sends notifications or alerts to the user for watering or other urgent plant care activities.

- **Log Plant Health Data**
  - Saves the combined care and sensor log into a database collection named `plant_health_logs`.
  - This enables history tracking and performance evaluation.

---

### User Plant Image Upload & AI Disease Detection

- **User Upload Plant Image**
  - HTTP request node to accept user-uploaded images of plants.
  
- **AI Identify Plant Disease**
  - Custom AI model node configured with `plant-disease-detection`.
  - Analyzes the uploaded image to detect potential diseases.

- **Generate Treatment Suggestions**
  - Uses GPT-4 to interpret disease detection results.
  - Provides detailed treatment and care recommendations based on the identified disease.

- **Send Treatment Alert**
  - Notifies the user with the treatment advice to take immediate action to prevent plant deterioration.

---

## Connections and Flow

- Sensor nodes feed their measurements into the **Aggregate Sensor Data** node.
- Aggregated data is analyzed by **Generate Care Recommendations**.
- Recommendations feed into **Create Watering Schedule and Alerts**, which in turn triggers:
  - Automated watering via the **Trigger Watering Workflow** node.
  - User notifications via the **Send Care Alert** node.
  - Data logging via the **Log Plant Health Data** node.
  
- The plant image upload triggers the disease detection nodes in sequence:
  - **User Upload Plant Image** → **AI Identify Plant Disease** → **Generate Treatment Suggestions** → **Send Treatment Alert**

---

## Key Features

- **Automated Sensor Monitoring:** Regular and automated collection of soil moisture, light level, and temperature data.
- **AI-Powered Recommendations:** Utilizes state-of-the-art GPT-4 models for personalized care advice based on live sensory data.
- **Advanced Scheduling:** Automatically derives and schedules plant watering tasks using AI.
- **Disease Detection:** Employs AI to identify plant diseases from user-uploaded images.
- **Treatment Guidance:** AI-generated treatment plans customized to detected plant diseases.
- **Alerts & Notifications:** Keeps users informed with timely notifications for watering and treatment measures.
- **Data Logging:** Records all plant health data for historical analysis and trend monitoring.
- **Extensibility:** Integrates with additional workflows like automated watering.

---

## Prerequisites

- IoT-enabled sensors (soil moisture, light, temperature) compatible with the system.
- Access to OpenAI GPT-4 API with necessary credentials.
- A plant disease detection AI model API.
- Notification service integration for alerts.
- A database to store plant health logs.
- An HTTP endpoint to upload plant images.

---

## Usage Instructions

1. **Setup IoT Sensors:** Connect soil moisture, light level, and temperature sensors to your smart home system.
2. **Configure AI APIs:** Insert API keys and configure access to OpenAI GPT-4 and disease detection models.
3. **Deploy Workflow:** Import this workflow into your automation platform (e.g., n8n).
4. **Upload Plant Images:** Use the HTTP endpoint to submit images of your plants for disease analysis.
5. **Monitor & Follow Alerts:** Receive care recommendations and alerts; follow AI-generated watering schedules.
6. **Review Logs:** Access the database collection `plant_health_logs` to review historical data.

---

## Summary

This workflow creates an intelligent assistant that not only monitors your indoor plants in real-time but also leverages powerful AI models to deliver actionable care instructions and automate routine plant maintenance tasks. Additionally, it helps diagnose plant diseases early from images and suggests treatments, ensuring your indoor garden thrives.