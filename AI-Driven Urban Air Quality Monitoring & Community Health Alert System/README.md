# AI-Powered Urban Air Quality Optimization & Community Health Advisory

This workflow provides an end-to-end automated system to monitor urban air quality, analyze pollution patterns using AI-heuristics, generate personalized health and environmental recommendations, and send community alert emails to local residents.

---

## Workflow Overview

The workflow fetches the latest air quality data for a specified city, processes and analyzes pollution indicators such as PM2.5, creates custom health advisories based on air quality severity, and delivers alert notifications to community members.

---

## Nodes Breakdown

### 1. Fetch Air Quality Data
- **Type**: HTTP Request  
- **Purpose**: Fetches real-time air quality data from the OpenAQ API.  
- **API Endpoint**: `https://api.openaq.org/v2/latest?city=YourCityName`  
- **Output**: JSON data containing measurements of pollutants from multiple locations in the city.

### 2. Parse and Structure Data
- **Type**: Function  
- **Purpose**: Parses the raw API response and structures pollutant data per location.  
- **Details**:
  - Extracts pollutant parameters (e.g., pm25, pm10, co, no2, etc.) and their values.
  - Preserves location, city name, and geographic coordinates.
  - Outputs an array of simplified JSON objects, each representing pollutant data tied to a specific monitoring location.

### 3. AI Analyze Pollution Patterns
- **Type**: Function  
- **Purpose**: Simulates AI-driven analysis on pollution patterns and severity levels.  
- **Logic**:
  - Focuses primarily on PM2.5 concentration as the severity metric.
  - Severity Levels:
    - **Low**: PM2.5 ≤ 35 µg/m³
    - **Moderate**: 35 < PM2.5 ≤ 75 µg/m³
    - **High**: PM2.5 > 75 µg/m³
  - Suggests corresponding alert messages based on severity:
    - Low: "Air quality is good. Enjoy your outdoor activities!"
    - Moderate: "Air quality is moderate. Sensitive groups should reduce prolonged exertion outdoors."
    - High: "Air quality is poor. Everyone should limit outdoor activities."

### 4. Generate Health & Environmental Recommendations
- **Type**: Function  
- **Purpose**: Generates personalized health advice and environmental actions for each location based on analyzed pollution severity.  
- **Logic**:
  - For **High** severity:
    - Wear N95 masks when outside.
    - Use indoor air purifiers.
    - Avoid outdoor exercise.
  - For **Moderate** severity:
    - Consider masks during extended outdoor activities.
    - Sensitive individuals should keep windows closed.
  - For **Low** severity:
    - Air quality is safe; maintain a healthy lifestyle.
  - Additional environmental recommendations based on PM2.5:
    - PM2.5 > 35: Support local traffic and emission reduction initiatives.
    - PM2.5 ≤ 35: Participate in green activities and tree planting.

### 5. Send Community Alert Email
- **Type**: Email Send  
- **Purpose**: Sends customized air quality alerts and recommendations to residents of each monitored location.  
- **Email Configuration**:
  - **From**: alerts@urbanhealth.ai  
  - **To**: Automatically generated for each location as `<LocationName>_residents@example.com` (replace domain as needed)  
  - **Subject**: `Community Air Quality Alert: <Severity> Level`  
  - **Body**: Includes location name, pollution severity, PM2.5 value, alert message, and detailed recommendations.

---

## Data Flow & Connections

1. **Fetch Air Quality Data** → outputs raw JSON to →  
2. **Parse and Structure Data** → outputs structured pollutant data to →  
3. **AI Analyze Pollution Patterns** → outputs severity and alert messages to →  
4. **Generate Health & Environmental Recommendations** → outputs advisories and environment actions to →  
5. **Send Community Alert Email** → sends notifications to residents.

---

## Setup Instructions

- Replace `YourCityName` in the HTTP Request node URL with the target city (case-sensitive as required by OpenAQ API).
- Adjust the email addresses' domain in the email send node (`{{$json["location"] + "_residents@example.com"}}`) to match your actual recipient domain.
- Configure SMTP/email credentials within your n8n instance for the Email Send node to function properly.
- Activate the workflow to enable automated periodic runs (via n8n scheduling or manual triggers).

---

## Extensibility Suggestions

- Integrate additional pollutant parameters or external data sources for enriched analysis.
- Enhance severity analysis using machine learning models or real historical trend analysis.
- Extend recipient lists to local authorities or public health organizations.
- Provide multi-language support and mobile push notifications.

---

## Summary

This AI-Powered Urban Air Quality Optimization workflow empowers communities with timely insights and actionable health guidance for mitigating pollution risks, leveraging open data and automation for proactive environmental and public health management.