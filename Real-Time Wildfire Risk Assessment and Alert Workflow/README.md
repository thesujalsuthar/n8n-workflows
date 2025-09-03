# Real-Time Wildfire Risk Alert System

This n8n workflow provides a comprehensive solution for real-time wildfire risk assessment and alerting by integrating weather data, satellite imagery, and local sensor readings. It calculates a dynamic risk score and triggers multi-channel notifications and incident reporting when risk thresholds are exceeded.

---

## Workflow Overview

### 1. Fetch Weather Data  
- **Node:** `Fetch Weather Data`  
- **Service:** OpenWeather API  
- **Purpose:** Retrieve current weather conditions for a specified region using geographic coordinates (`regionCoordinates`).  
- **Inputs:**  
  - Location coordinates (`regionCoordinates`)  
  - OpenWeather API key (secured via credentials)  
- **Outputs:** Weather data including temperature, humidity, and wind speed.

### 2. Fetch Satellite Imagery Metadata  
- **Node:** `Fetch Satellite Imagery Metadata`  
- **Service:** Satellite Imagery Service API  
- **Purpose:** Obtain the latest satellite imagery metadata within a bounding box (`regionBoundingBox`), filtering for cloud coverage ≤ 10%.  
- **Authentication:** OAuth2  
- **Parameters:**  
  - `bbox`: Region bounding box coordinates  
  - `cloud_coverage_max`: 10%  
- **Outputs:** Metadata containing detected hotspots and other relevant environmental indicators.

### 3. Fetch Local Sensor Data  
- **Node:** `Fetch Local Sensor Data`  
- **Service:** Local sensor data API  
- **Purpose:** Retrieve sensor readings collected within the last 15 minutes for the target region (`regionId`).  
- **Authentication:** OAuth2  
- **Parameters:**  
  - `region_id`  
  - `last_minutes` = 15  
- **Outputs:** Array of sensor data including smoke levels and dryness index.

### 4. Calculate Risk Score  
- **Node:** `Calculate Risk Score`  
- **Type:** Function node (JavaScript)  
- **Purpose:** Combines weather data, satellite imagery hotspots, and local sensor indicators to calculate a wildfire risk score out of 100.  
- **Logic Highlights:**  
  - Temperature thresholds contribute up to 30 points  
  - Low humidity adds up to 30 points  
  - High wind speed adds up to 30 points  
  - Hotspot count from satellite imagery adds up to 40 points  
  - Smoke detection from sensors adds 30 points  
  - Dryness index contributes up to 20 points  
  - Final score is capped at 100  

- **Outputs:**  
  - `riskScore`: Final calculated risk score  
  - Environment details: temperature, humidity, wind speed, hotspot count, smoke detection status, dryness index

### 5. Check Risk Threshold  
- **Node:** `Check Risk Threshold`  
- **Type:** If node  
- **Purpose:** Evaluates if the calculated `riskScore` is greater than or equal to a threshold value (default 60, can be overridden dynamically via `dynamicRiskThreshold`).  
- **Outputs:**  
  - **True branch:** Executes notification and reporting steps  
  - **False branch:** Ends workflow, no alerts sent

### 6. Send Alerts (Email, SMS, Push Notification)  
- **Nodes:**  
  - `Send Email Alert` (email)  
  - `Send SMS Alert` (SMS via Twilio)  
  - `Send Push Notification` (HTTP POST to push notification service)  
- **Purpose:** Notify stakeholders and emergency contacts about the wildfire risk. Notifications contain risk score, region name, and relevant environmental data.  
- **Recipients:** Extracted from workflow input JSON (`emailRecipients`, `smsRecipients`)  
- **Message Contents:**  
  - Risk score and region  
  - Current temperature, humidity, wind speed  
  - Hotspot count, smoke detection, dryness index  
  - Safety recommendations  

### 7. Generate Incident Report  
- **Node:** `Generate Incident Report` (Function node)  
- **Purpose:** Creates a structured incident report with all related data and current timestamp for record-keeping and analysis.

### 8. Store Incident Report  
- **Node:** `Store Incident Report` (MongoDB insert)  
- **Purpose:** Stores the generated incident report in a MongoDB collection named `incidents`.  
- **Credentials:** MongoDB connection (`MongoDB Wildfire Incidents`)

---

## Input Data Structure Requirements

- **Region and Location Data:**  
  - `regionCoordinates`: Coordinates (latitude, longitude) used by OpenWeather node  
  - `regionBoundingBox`: Bounding box coordinates for satellite imagery query  
  - `regionId`: Identifier for local sensor data query and incident reporting  
  - `regionName`: Human-readable region name for notifications

- **Recipients:**  
  - `emailRecipients`: One or more email addresses for alerting  
  - `smsRecipients`: One or more phone numbers for SMS alerts  

- **Optional:**  
  - `dynamicRiskThreshold`: Number to override default risk threshold (60)

---

## Credentials Setup

- **OpenWeather API:** API key credential named `OpenWeather API`  
- **OAuth2 Credentials:** For satellite imagery and local sensor APIs  
- **Twilio SMS:** Twilio credentials named `Twilio SMS` for sending SMS alerts  
- **MongoDB:** Database credential named `MongoDB Wildfire Incidents`

---

## Execution Flow Summary

1. Retrieve current weather, satellite imagery metadata, and local sensor data concurrently.
2. Aggregate all data in `Calculate Risk Score` node and compute wildfire risk score.
3. Evaluate risk score against threshold:
   - If risk score ≥ threshold, trigger alerts via Email, SMS, Push Notification.
   - Generate and store an incident report in MongoDB.
   - If risk score < threshold, no alerts are sent.
   
---

## Customization Tips

- Adjust the risk scoring logic in the `Calculate Risk Score` node to fit specific environmental factors or data sources.
- Update API endpoints, query parameters, or authentication as needed for your data providers.
- Modify notification templates with additional details or formatting for stakeholders.
- Configure alert thresholds dynamically using workflow variables or external inputs.

---

## Notes

- Ensure all referenced credentials are properly created and authorized in your n8n instance.
- Verify the external APIs used (satellite imagery, local sensors) support OAuth2 authentication.
- The push notification service URL and format must match your notification infrastructure.

---

This workflow enables proactive wildfire risk management by automating data collection, analysis, alerting, and reporting in a robust and extensible manner.