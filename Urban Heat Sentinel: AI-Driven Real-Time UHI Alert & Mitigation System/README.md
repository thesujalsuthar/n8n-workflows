# AI-Powered Real-Time Urban Heat Island Mitigation Alert System

## Overview  
This workflow is designed to monitor urban heat island (UHI) effects in real time using environmental sensor data and weather information. It calculates a UHI index, determines alert levels, generates mitigation recommendations, and automatically sends notifications via email, SMS, and Slack to relevant stakeholders for timely intervention.

---

## Workflow Breakdown

### 1. Get Environmental Sensor Data  
- **Node:** `Get Environmental Sensor Data`  
- **Type:** HTTP Request (GET)  
- **Description:** Fetches real-time temperature data from multiple urban sensors via an external environmental sensor API endpoint:  
  `https://api.environmentalsensors.example.com/realtime-data`  
- **Output:** JSON containing temperature readings at various urban locations.

### 2. Get Weather Data  
- **Node:** `Get Weather Data`  
- **Type:** HTTP Request (GET)  
- **Description:** Retrieves current ambient weather data for the specified city using OpenWeatherMap API.  
- **API URL:** `https://api.openweathermap.org/data/2.5/weather`  
- **Query Parameters:**  
  - `q`: City name (e.g., `CityName`)  
  - `appid`: API Key (stored securely as credentials)  
  - `units`: Metric units (Celsius)  
- **Output:** JSON including ambient temperature, humidity, wind speed, and other weather details.

### 3. Analyze UHI Index and Generate Recommendations  
- **Node:** `Analyze UHI Index and Generate Recommendations`  
- **Type:** Function (JavaScript code)  
- **Purpose:** Processes input data to:  
  - Calculate the Urban Heat Island (UHI) index as the difference between average urban sensor temperature and ambient temperature.  
  - Determine alert level based on UHI index thresholds:  
    - Critical (≥ 7)  
    - High (≥ 4)  
    - Moderate (≥ 2)  
    - Normal (< 2)  
  - Generate mitigation recommendations tailored to the alert level.  
- **Outputs:**   
  - `uhiIndex` (numeric)  
  - `alertLevel` (string)  
  - `recommendations` (array of strings)  
  - `avgUrbanTemp` (number)  
  - `ambientTemp` (number)

### 4. Send Email Alert  
- **Node:** `Send Email Alert`  
- **Type:** Email Send  
- **Description:** Sends detailed UHI alert emails to city planners and residents.  
- **Email Details:**  
  - **From:** `alerts@citydomain.gov`  
  - **To:** `city.planners@citydomain.gov, residents@citydomain.gov`  
  - **Subject:** Includes alert level dynamically (e.g., "Urban Heat Island Alert: Critical Level Detected")  
  - **Content:** Provides UHI index, temperatures, alert level, and actionable recommendations in both plain text and HTML formats.  
- **Credentials:** Uses configured SMTP server for sending emails.

### 5. Send SMS Alert  
- **Node:** `Send SMS Alert`  
- **Type:** Twilio SMS  
- **Description:** Sends SMS alerts to mobile recipients based on alert severity.  
- **Recipients:**  
  - Critical alert: Phone number +15550000001  
  - Other alerts: Phone number +15550000002  
- **Message Contents:** UHI alert level, index, and recommendations.  
- **Credentials:** Uses Twilio API with the configured account.

### 6. Send Slack Notification  
- **Node:** `Send Slack Notification`  
- **Type:** Slack Message  
- **Description:** Posts UHI alert notifications in a Slack channel for city officials and urban planners.  
- **Channel:** `urban-heat-alerts`  
- **Message:** Includes alert level, UHI index, and line-item recommendations formatted for Slack markdown.  
- **Credentials:** Uses configured Slack Workspace API token.

---

## Data Flow & Connections  
- The workflow starts with fetching environmental sensor data.  
- Once sensor data is successfully retrieved, weather data is gathered for the same city.  
- Both datasets are passed into the analysis function to compute the UHI index and corresponding alerts/recommendations.  
- Results from the analysis node trigger parallel notification sending across email, SMS, and Slack channels.

---

## Prerequisites  
- Environmental sensor API endpoint must be accessible and return data in expected JSON format containing temperature readings per location.  
- OpenWeatherMap API key stored securely in n8n credentials as `OpenWeatherMap API`.  
- SMTP server credentials configured and stored as `City SMTP Server`.  
- Twilio account credentials stored as `Twilio Account` for SMS sending.  
- Slack workspace token stored as `Slack Workspace` with permissions to post in the `urban-heat-alerts` channel.  
- Replace `"CityName"` in `Get Weather Data` node with the actual city to monitor.

---

## Usage  
1. Import this workflow into your n8n instance.  
2. Configure all required credentials and sensitive parameters as noted above.  
3. Adjust the city name in the `Get Weather Data` node as needed.  
4. Activate the workflow to enable continuous monitoring and alerts.  
5. Monitor emails, SMS, and Slack channels for real-time UHI alerts and mitigation guidance.

---

## Alert Levels and Corresponding Actions

| UHI Index Range | Alert Level | Key Recommendations                                             |
|-----------------|-------------|-----------------------------------------------------------------|
| ≥ 7             | Critical    | - Activate emergency cooling centers  
                                   - Deploy water misting systems  
                                   - Issue heat warnings to residents and vulnerable groups |
| ≥ 4 and < 7     | High        | - Increase green cover in hotspots  
                                   - Promote reflective/ cool roofing  
                                   - Advise hydration and limit outdoor activities          |
| ≥ 2 and < 4     | Moderate    | - Monitor temperature closely  
                                   - Encourage neighborhood vegetation planting             |
| < 2             | Normal      | - Maintain usual precautions                                  |

---

## Notes  
- The UHI index is calculated as the difference between average urban temperature (from sensors) and ambient weather temperature.  
- Notifications adapt dynamically based on alert severity ensuring appropriate audience and action.  
- This system supports proactive urban heat management and public safety during extreme heat events.

---

End of documentation.