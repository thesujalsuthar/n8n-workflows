# SmartHome AI Energy Optimizer & Alert System

## Overview
This workflow is designed to optimize home energy consumption and provide timely alerts using AI-driven analytics. It collects real-time energy data, weather forecasts, and user behavior patterns to analyze and detect abnormal usage, then sends notifications with recommendations via SMS and email.

---

## Workflow Components

### 1. Get Smart Meter Data
- **Type:** HTTP Request  
- **Authentication:** Basic Auth  
- **Purpose:** Retrieves real-time energy consumption data from the smart meter API at `<SMART_METER_API_ENDPOINT>`.  
- **Response Format:** JSON  
- **Output:** Returns energy usage readings and timestamps.

### 2. Get Weather Forecast
- **Type:** HTTP Request  
- **Authentication:** None  
- **Purpose:** Fetches current and forecasted weather data from `<WEATHER_FORECAST_API_ENDPOINT>`.  
- **Response Format:** JSON  
- **Output:** Provides weather conditions such as temperature, humidity, etc.

### 3. Get User Behavior Data
- **Type:** HTTP Request  
- **Authentication:** Basic Auth  
- **Purpose:** Obtains user activity and presence data from `<USER_BEHAVIOR_API_ENDPOINT>`.  
- **Response Format:** JSON  
- **Output:** Indicates if the user is home, away, or other behavior patterns.

### 4. Merge Data (Function Node)
- **Type:** Function  
- **Purpose:** Combines the outputs of the three previous nodes into a single data object for unified analysis.  
- **Process:**
  - Extracts `energyUsage` and `timeStamps` from Smart Meter data.
  - Packages weather and behavior data.
  - Returns a consolidated JSON object with all collected data.

### 5. AI Energy Analytics (Function Node)
- **Type:** Function  
- **Purpose:** Performs AI-driven analytics and generates insights and recommendations.  
- **Process:**
  - Uses a simple heuristic to detect abnormal energy usage (if any usage value exceeds a defined threshold, e.g., 1000 watts).
  - Incorporates weather data to suggest behavioral changes (e.g., using fans instead of AC if temperature is high).
  - Uses user behavior data to provide context-specific recommendations (e.g., eco HVAC mode if user is away).
- **Output:**  
  - `abnormalUsage`: Boolean flag for unusual consumption.  
  - `recommendations`: List of energy-saving suggestions.

### 6. Send SMS Notification (Twilio Node)
- **Type:** Twilio SMS  
- **Purpose:** Sends real-time SMS alerts if abnormal usage is detected, including actionable recommendations.  
- **Parameters:**
  - `number`: Phone number retrieved dynamically via `$json["phoneNumber"]`.
  - `message`: Composed alert and recommendation text, conditionally included based on analysis results.

### 7. Send Email Notification (Email Node)
- **Type:** Email Send  
- **Purpose:** Sends detailed email alerts with energy usage warnings and recommendations.  
- **Parameters:**
  - `toEmail`: User's email address (`$json["email"]`).
  - `subject`: Static subject line indicating a SmartHome alert.
  - `text`: Email body highlighting detected abnormalities and recommendations, formatted for readability.

---

## Data Flow Summary

1. **Data Retrieval:**  
   The workflow starts by simultaneously requesting data from the smart meter, weather forecast API, and user behavior API.

2. **Data Aggregation:**  
   The "Merge Data" node consolidates these inputs into a comprehensive dataset.

3. **Analysis:**  
   The "AI Energy Analytics" function analyzes combined data to detect abnormalities and suggest actions.

4. **Notification:**  
   Based on analytics, SMS and email notifications are dispatched to inform the user.

---

## Configuration Notes

- Replace placeholder API endpoints (`<SMART_METER_API_ENDPOINT>`, `<WEATHER_FORECAST_API_ENDPOINT>`, `<USER_BEHAVIOR_API_ENDPOINT>`) with actual URLs.
- Provide correct authentication credentials for APIs requiring Basic Auth.
- Ensure Twilio credentials and email service configurations are properly set up.
- Adjust the energy usage threshold in the AI analytics function as per your home's appliance specifications.
- The phone number and email address must be present in the incoming JSON data for notifications to succeed.

---

## Activation

- Set the workflow to **active** to start automatic data polling, analysis, and alerting.
- Monitor logs for successful API calls and notifications.

---

This SmartHome AI Energy Optimizer & Alert System empowers efficient energy usage and proactive household energy management through intelligent monitoring and user-centric alerting.