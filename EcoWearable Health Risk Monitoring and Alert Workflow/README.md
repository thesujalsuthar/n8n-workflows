# EcoWearable: Real-Time Personal Environmental Exposure and Health Alert System

## Overview

EcoWearable is a comprehensive workflow designed to monitor real-time personal environmental exposure alongside physiological data from a wearable device. It integrates air quality, pollen, UV exposure, and wearable vitals to assess health risks and sends timely alerts via email and SMS. This system aims to help users minimize exposure to harmful environmental factors and protect their health by providing actionable recommendations.

---

## Workflow Components

### 1. Get Wearable Data

- **Node Type:** Wearable Device (n8n built-in)
- **Purpose:** Fetches the latest sensor data from the user's wearable device.
- **Parameters:**
  - `deviceId`: Unique identifier of the wearable device.
  - `resource`: `sensorData`
  - `operation`: `getLatest`
- **Data Retrieved:** Includes heart rate, UV exposure, and geographic location (latitude & longitude).

### 2. Get Local Air Quality Data

- **Node Type:** HTTP Request
- **Purpose:** Retrieves the latest air quality data near the user's current location.
- **API Used:** [OpenAQ API](https://api.openaq.org/v2/latest)
- **Parameters:**
  - Query `coordinates`: Uses latitude and longitude from wearable data.
  - Radius: 5000 meters.
- **Response Format:** JSON

### 3. Get Local Pollen and UV Data

- **Node Type:** HTTP Request
- **Purpose:** Fetches local pollen counts and UV index conditions.
- **API Used:** Weather.com Current Conditions API
- **Parameters:**
  - `geocode`: Latitude and longitude from wearable data.
  - `format`: JSON
  - `apiKey`: User's Weather.com API key.
- **Response Format:** JSON

### 4. AI Health Risk Assessment

- **Node Type:** Function
- **Purpose:** Analyzes the collected data from wearable, air quality, and environmental APIs to calculate an overall health risk score. Generates alerts and actionable recommendations.
- **Risk Factors Evaluated:**
  - **Heart Rate:**
    - > 100 bpm: +2 risk points
    - 85-100 bpm: +1 risk point
  - **Air Pollution Index:**
    - > 100 total pollution value: +3 risk points
    - 50-100 pollution value: +1 risk point
  - **Pollen Count:**
    - > 50: +2 risk points
  - **UV Exposure:**
    - Wearable UV exposure > 5 or UV Index > 7: +2 risk points
- **Risk Score Interpretation:**
  - **6 or above:** High risk — avoid outdoor activities, stay hydrated, seek medical advice.
  - **3 to 5:** Moderate risk — minimize exposure, use protective gear.
  - **1 to 2:** Low risk — stay aware and take preventive measures.
  - **0:** All clear.

### 5. Send Email Alert

- **Node Type:** Email Send
- **Purpose:** Sends an email notification to the user with the risk alert and recommendations.
- **Parameters:**
  - `fromEmail`: alerts@ecowearable.com
  - `toEmail`: User's email (defaults to user@example.com if not set)
  - `subject`: EcoWearable Health Alert
  - `text`: Dynamic message including alert and recommendations.

### 6. Send SMS Alert

- **Node Type:** Twilio
- **Purpose:** Sends an SMS alert to the user with the health risk assessment and recommendations.
- **Parameters:**
  - `to`: User’s phone number (defaults to +1234567890 if not set)
  - `message`: Dynamic text including alert and recommendations.

---

## Workflow Execution Flow

1. The workflow starts by acquiring the latest physiological and location data from the user’s wearable device.
2. It concurrently fetches the local air quality data and local pollen/UV data by using the user’s current GPS coordinates.
3. The gathered data sets are aggregated and processed by an AI-driven function that assesses the overall health risk based on combined environmental and physiological inputs.
4. Depending on the risk score, personalized alerts with health recommendations are generated.
5. These alerts are then sent to the user automatically via both email and SMS for immediate notification.

---

## Setup Instructions

1. **Wearable Device ID:** Replace `"Your_Wearable_Device_ID"` in the "Get Wearable Data" node with your actual device ID.
2. **Weather API Key:** Input your Weather.com API key in the "Get Local Pollen and UV Data" node under `apiKey`.
3. **User Contact Details:**
   - Email address: Ensure the user’s email is passed as `userEmail` in the workflow data, or update the default email in the "Send Email Alert" node.
   - Phone number: Ensure the user’s phone number is passed as `userPhone` in the workflow data, or update the default phone number in the "Send SMS Alert" node.
4. **Email Sender Setup:** Configure the sending email account (alerts@ecowearable.com) with the appropriate SMTP credentials in n8n.
5. **Twilio Setup:** Configure Twilio credentials in the SMS Alert node for sending messages.

---

## Notes

- The system depends on real-time location data from the wearable to fetch accurate environmental parameters.
- Adjust risk thresholds and parameters in the AI Health Risk Assessment node according to specific user health profiles or environmental standards.
- Ensure proper API usage compliance and rate limits for OpenAQ and Weather.com services.

---

By combining personal physiological data and environmental monitoring, EcoWearable empowers users with actionable health insights to safeguard their well-being proactively.