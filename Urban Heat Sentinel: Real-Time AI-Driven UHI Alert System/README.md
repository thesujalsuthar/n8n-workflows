# AI-Powered Real-Time Urban Heat Island Alert System

## Overview

This workflow is designed to monitor urban temperature data in real-time, detect Urban Heat Island (UHI) anomalies, generate actionable recommendations based on severity, and send alert emails to relevant stakeholders. The system runs every 5 minutes, providing timely heat alerts to help mitigate risks associated with extreme urban heat.

---

## Workflow Components

### 1. Trigger - Every 5 Minutes

- **Node Type:** Cron Trigger
- **Schedule:** Executes every 5 minutes (`*/5 * * * *`)
- **Purpose:** Initiates the workflow at regular intervals to ensure up-to-date data monitoring.

---

### 2. Fetch Urban Temperature Data

- **Node Type:** HTTP Request
- **API Endpoint:** OpenWeatherMap Current Weather Data API
- **Request Details:**
  - URL Template: `https://api.openweathermap.org/data/2.5/weather?q=CityName&appid=YOUR_API_KEY&units=metric`
  - **Note:** Replace `CityName` with the target city and `YOUR_API_KEY` with your OpenWeatherMap API key.
- **Purpose:** Retrieves the latest temperature and weather data for the specified urban location in Celsius.

---

### 3. Detect Heat Island Anomaly

- **Node Type:** Function
- **Function Logic:**
  - Extracts current temperature from API response (`data.main.temp`).
  - Compares current temperature to a baseline temperature (default 30°C).
  - Detects anomaly if temperature exceeds baseline by at least 5°C.
  - Calculates anomaly severity based on the difference.
  - Outputs JSON including:
    - `currentTemp`
    - `baselineTemp`
    - `anomalyDetected` (boolean)
    - `anomalySeverity` (°C above baseline)
    - `location`
    - `timestamp` (ISO format)
- **Purpose:** Identifies significant urban heat anomalies indicating the presence of an Urban Heat Island effect.

---

### 4. Generate Actionable Recommendations

- **Node Type:** Function
- **Function Logic:**
  - Only proceeds if an anomaly is detected.
  - Determines severity and generates tailored recommendations:
    - **Severity ≥ 8°C:**
      - Activate cooling centers immediately.
      - Issue urgent public heat warnings.
      - Increase green space watering frequency.
    - **Severity between 5°C and 8°C:**
      - Advise residents to stay hydrated and avoid outdoor activity during peak hours.
      - Check vulnerable populations for heat stress.
      - Deploy mobile misting stations.
    - **Severity < 5°C (but anomaly detected):**
      - Monitor temperatures closely.
      - Encourage use of shade and light clothing.
  - Outputs extended JSON including recommendations.
- **Purpose:** Translates data insights into practical measures to address heat risks.

---

### 5. Send Alert Email

- **Node Type:** Email Send
- **Email Configuration:**
  - **From:** alerts@urbanheatmonitor.com
  - **To:** local.authorities@example.com, residents@example.com
  - **Subject:** `Urban Heat Island Alert - {{ $json.location }} - {{ $json.timestamp }}`
  - **Body:**
    ```
    Heat anomaly detected in {{ $json.location }}.

    Current Temperature: {{ $json.currentTemp }}°C
    Baseline Temperature: {{ $json.baselineTemp }}°C
    Severity Level: {{ $json.anomalySeverity }}°C above baseline

    Recommended Actions:
    - {{ $json.recommendations.join('\n- ') }}

    Please take necessary precautions to mitigate heat risks.
    ```
- **Purpose:** Notifies local authorities and residents of detected heat anomalies along with recommended actions.

---

## How It Works

1. Every 5 minutes, the workflow triggers automatically.
2. The system fetches the latest temperature data for the specified city.
3. The temperature is analyzed to detect any significant heat anomalies compared to a baseline.
4. If an anomaly is identified, the system generates a set of actionable recommendations based on the severity.
5. An alert email comprising the details and recommendations is sent to predefined recipients.
6. If no anomaly is detected, the workflow terminates without sending notifications to reduce unnecessary noise.

---

## Setup Instructions

1. **Configure the API Request:**
   - Replace `CityName` with the target city in the **Fetch Urban Temperature Data** node.
   - Replace `YOUR_API_KEY` with your valid OpenWeatherMap API key.

2. **Customize Email Recipients:**
   - Modify the **To Email** field in the **Send Alert Email** node to include relevant local authorities and resident groups.

3. **Adjust Thresholds & Baseline (Optional):**
   - Update `heatIslandThreshold` and `baselineTemp` values in the **Detect Heat Island Anomaly** node as per local climate and historic data.

4. **Enable and Activate Workflow:**
   - Set the workflow as active in n8n to start automated monitoring and alerts.

---

## Notes

- The baseline temperature and anomaly threshold are initial values and should be customized for each urban environment.
- Emails should be tested with valid SMTP settings and verified sender addresses.
- Additional improvements can include integrating more detailed weather parameters or expanding alert channels.

---

## Summary

This workflow provides a scalable and automated solution for monitoring urban heat conditions, quickly identifying dangerous heat island effects, and alerting communities to take timely action, thereby enhancing urban heat resilience and public safety.