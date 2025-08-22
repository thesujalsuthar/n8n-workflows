# GreenCommute AI Scheduler

## Overview

The GreenCommute AI Scheduler is an automated workflow designed to provide users with optimal eco-friendly commute options based on real-time public transport schedules, weather forecasts, and air quality data. The system further monitors transit delays and air quality alerts to notify users instantly, ensuring a safe and sustainable daily commute.

---

## Workflow Components

### 1. Set Initial Input Data

- **Node Type:** Set
- **Purpose:** Captures user input including origin, destination, date/time of travel, geographic coordinates (latitude and longitude), and user email.
- **Details:** This node initializes the key parameters that are subsequently fed into the external API calls and notification nodes.

### 2. Get Public Transport Schedules

- **Node Type:** HTTP Request (GET)
- **API Endpoint:** `https://api.publictransport.example.com/v1/schedules`
- **Parameters:**
  - `origin`: Starting location from user input
  - `destination`: Ending location from user input
  - `datetime`: Desired travel datetime from user input
- **Response Format:** JSON
- **Purpose:** Fetches detailed real-time public transport schedules matching the user’s commute parameters.

### 3. Get Weather Forecast

- **Node Type:** HTTP Request (GET)
- **API Endpoint:** `https://api.weather.example.com/v1/forecast`
- **Parameters:**
  - `lat`: Latitude from user input
  - `lon`: Longitude from user input
  - `datetime`: Travel datetime from user input
- **Response Format:** JSON
- **Purpose:** Retrieves weather forecast data to evaluate conditions that may impact certain modes of transport such as biking or walking.

### 4. Get Air Quality Data

- **Node Type:** HTTP Request (GET)
- **API Endpoint:** `https://api.airquality.example.com/v1/airquality`
- **Parameters:**
  - `lat`: Latitude from user input
  - `lon`: Longitude from user input
- **Response Format:** JSON
- **Purpose:** Collects air quality index (AQI) data to inform route suitability for outdoor activities and breathing-sensitive individuals.

### 5. Analyze & Rank Options

- **Node Type:** Function
- **Purpose:** Merges schedules, weather, and air quality data to analyze and rank the various commute options based on an eco-score.
- **Logic:**
  - Assigns lower scores to cleaner modes of transportation (`walk`, `bike`, `subway`, `bus`) reflecting their carbon footprint.
  - Adds penalties for poor weather conditions (e.g., precipitation above 0.2mm) affecting bike/walking options.
  - Penalizes options if air quality index (AQI) exceeds 100, impacting outdoor travel modes.
  - Penalizes delayed transit options.
  - Outputs a sorted list of commute options ranked from best (lowest eco-score) to worst.

### 6. Detect Transit Delays

- **Node Type:** IF
- **Condition:** Checks if any selected transit options have `delayed` set to `true`.
- **Purpose:** Flags transit delay events to trigger immediate user notifications.

### 7. Detect Air Quality Alerts

- **Node Type:** IF
- **Condition:** Checks if air quality index (AQI) is greater than 150.
- **Purpose:** Detects hazardous air quality conditions and triggers alert notifications.

### 8. Send Route Recommendation Email

- **Node Type:** Email Send
- **From:** `no-reply@greencommute.ai`
- **To:** User email address from input data
- **Subject:** "GreenCommute: Best Eco-Friendly Route Recommendation"
- **Message:** Provides the user with the optimal eco-friendly route details including route name, mode of transit, estimated departure, and arrival times along with advisory notes.

### 9. Send Transit Delay Alert

- **Node Type:** Email Send
- **From:** `alerts@greencommute.ai`
- **To:** User email address
- **Subject:** "GreenCommute Alert: Transit Delay Detected"
- **Message:** Alerts the user about delays on the selected transit route and suggests considering alternate options.

### 10. Send Air Quality Advisory

- **Node Type:** Email Send
- **From:** `alerts@greencommute.ai`
- **To:** User email address
- **Subject:** "GreenCommute Alert: Poor Air Quality Advisory"
- **Message:** Warns the user about unhealthy air quality, recommending indoor alternatives or precautions.

---

## Workflow Execution Flow

1. **User Input Setup**
   - User inputs origin, destination, datetime, location coordinates, and email in the **Set Initial Input Data** node.

2. **Data Collection**
   - Parallel HTTP requests fetch public transport schedules, weather forecasts, and air quality data.

3. **Data Analysis**
   - The **Analyze & Rank Options** function receives all datasets, scores, and ranks commute options by eco-friendliness, factoring in environmental and operational constraints.

4. **Primary Notification**
   - A route recommendation email is sent to the user with the best commute option.

5. **Alerts Monitoring**
   - The workflow checks for transit delays and air quality alerts.
   - If any conditions are met, corresponding alert emails are triggered to notify the user promptly.

---

## Prerequisites

- Access to public transport, weather, and air quality APIs with appropriate API keys if required.
- A configured SMTP/email provider for sending emails.
- User inputs must include accurate location coordinates and valid email addresses.

---

## Customization & Extension

- Scoring logic in the **Analyze & Rank Options** node can be tailored to incorporate additional parameters such as user preferences (e.g., shortest time, accessibility).
- Additional notification channels (e.g., SMS, push notifications) can be integrated.
- Support for other transportation modes and alternative sources of environmental data can enhance recommendations.

---

## Summary

The GreenCommute AI Scheduler integrates multiple real-time data sources to empower users with personalized, eco-conscious commuting recommendations. By dynamically adapting to weather and air quality conditions and monitoring transit performance, it promotes healthier, cleaner, and more efficient urban travel.