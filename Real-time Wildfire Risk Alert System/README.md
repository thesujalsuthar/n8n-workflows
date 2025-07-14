# Real-time Wildfire Risk and Alert System

This workflow provides a system to assess wildfire risk in real time for specified locations and send alerts via SMS, email, and push notifications to subscribers.

---

## Workflow Overview

1. **Subscribers List**: Defines users with their geographic coordinates and contact details.
2. **Get Weather Data**: Fetches current weather conditions from OpenWeatherMap based on a subscriber's location.
3. **Get Satellite Fire Detection Data**: Queries NASA's Earth API for recent satellite-detected fire incidents near the subscriber's location.
4. **Risk Analysis**: Evaluates wildfire risk by analyzing temperature, wind speed, and presence of recent fires.
5. **Send SMS Alert**: Sends a wildfire risk alert via SMS using Twilio.
6. **Send Email Alert**: Sends a wildfire risk notification email.
7. **Send Push Notification**: Sends a push notification about the wildfire risk level.

---

## Nodes Description

### 1. Subscribers List  
- **Type**: Function  
- **Purpose**: Contains hardcoded subscriber data including latitude, longitude, phone number, email, and push notification ID.  
- **Sample Data**:
  ```json
  [
    {
      "latitude": 38.8951,
      "longitude": -77.0364,
      "userPhone": "+15550001111",
      "userEmail": "user1@example.com",
      "userPushId": "user1_push_id"
    },
    {
      "latitude": 34.0522,
      "longitude": -118.2437,
      "userPhone": "+15550002222",
      "userEmail": "user2@example.com",
      "userPushId": "user2_push_id"
    }
  ]
  ```

---

### 2. Get Weather Data  
- **Type**: HTTP Request  
- **API**: OpenWeatherMap Current Weather Data  
- **Parameters**:
  - Latitude & Longitude: From Subscriber List  
  - API Key: `<OPENWEATHERMAP_API_KEY>` (replace with your API key)  
- **Purpose**: Fetches current temperature and wind speed data for each subscriber location.

---

### 3. Get Satellite Fire Detection Data  
- **Type**: HTTP Request  
- **API**: NASA Earth API - Fire Detection Assets  
- **Parameters**:
  - Latitude & Longitude: From Subscriber List  
  - API Key: `<NASA_API_KEY>` (replace with your API key)  
  - Date Range: Past day (from yesterday to today)  
- **Purpose**: Retrieves fire detection data from satellite imagery indicating presence of fires near subscriber locations.

---

### 4. Risk Analysis  
- **Type**: Function  
- **Input**: Outputs from Get Weather Data and Get Satellite Fire Detection Data nodes.  
- **Logic**:
  - Converts temperature from Kelvin to Celsius.
  - Checks if the temperature is ≥ 35°C.
  - Checks if wind speed is ≥ 10 m/s.
  - Checks presence of detected fires.
  - Assigns wildfire risk level based on conditions:
    - **High**: Temperature AND wind speed thresholds met AND fire detected.
    - **Moderate**: Either temperature threshold OR fire detected.
    - **Low**: Otherwise.
- **Output**: JSON object containing:
  - `temperatureCelsius`
  - `windSpeed` (m/s)
  - `fireDetected` (boolean)
  - `riskLevel` (Low, Moderate, High)

---

### 5. Send SMS Alert  
- **Type**: Twilio  
- **Purpose**: Sends SMS wildfire risk alert to subscriber's phone number.  
- **Message Format**:  
  `Wildfire Risk Alert: Your area risk level is {riskLevel}. Stay alert and follow safety guidelines.`  
- **Credentials Required**: Twilio API Key  

---

### 6. Send Email Alert  
- **Type**: Email Send (SMTP)  
- **Purpose**: Emails wildfire risk alert to subscriber's email address.  
- **From Email**: `alerts@wildfirealerts.com`  
- **Subject**: `Wildfire Risk Alert`  
- **Message Content**:
  ```
  Dear user,

  Your area's current wildfire risk level is: {riskLevel}.
  Please stay prepared and monitor local emergency instructions.

  Stay safe,
  Wildfire Alert System
  ```  
- **Credentials Required**: SMTP email credentials  

---

### 7. Send Push Notification  
- **Type**: Pushover  
- **Purpose**: Sends a push notification with wildfire risk status to subscriber devices.  
- **Notification Title**: `Wildfire Risk Alert`  
- **Message**:  
  `Risk level for your area is currently {riskLevel}. Please remain vigilant.`  
- **Credentials Required**: Pushover API key  

---

## How Data Flows

1. **Subscribers List** feeds latitude, longitude, phone, email, and push IDs to downstream nodes.  
2. The subscriber's location parameters are used to fetch weather and satellite fire detection data concurrently.  
3. Results from both APIs are passed to Risk Analysis, which determines the wildfire risk level.  
4. The risk level is then sent as alerts to the subscriber via SMS, email, and push notifications.

---

## Setup Instructions

1. Replace placeholder API keys and credentials:
   - `<OPENWEATHERMAP_API_KEY>` for OpenWeatherMap API.
   - `<NASA_API_KEY>` for NASA Earth API.
   - `<CREDENTIAL_ID>` for Twilio, SMTP email, and Pushover credentials.

2. Modify the **Subscribers List** node to include actual user data.

3. Deploy and execute the workflow to automatically send wildfire risk alerts in real-time.

---

## Alert Criteria Summary

| Condition | Temperature (°C) | Wind Speed (m/s) | Fire Detected | Wildfire Risk Level |
|-----------|------------------|------------------|---------------|---------------------|
| High      | ≥ 35             | ≥ 10             | Yes           | High                |
| Moderate  | ≥ 35             | Any              | No or Yes     | Moderate            |
| Low       | < 35             | Any              | No            | Low                 |

---

## Notes

- Temperature is converted from Kelvin (API) to Celsius.
- Only fires detected within the past day are considered.
- Currently set for two example subscribers; scale by adding more users in Subscribers List.

---

End of documentation.