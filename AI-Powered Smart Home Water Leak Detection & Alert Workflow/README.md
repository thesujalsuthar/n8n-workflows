# AI-Powered Smart Home Water Leak Detection & Alert System

## Overview

This workflow implements a comprehensive AI-powered smart home water leak detection and alert system using n8n. It listens for water leak sensor data, filters leak events, logs the data, sends alerts via SMS and email, triggers a shut-off valve, and also provides an analytics dashboard for monitoring leak history.

---

## Workflow Components

### 1. Smart Water Sensor Webhook
- **Type:** Webhook
- **Purpose:** Receives real-time data from smart water sensors as HTTP requests.
- **Parameters:** Accepts JSON data containing sensor information such as `sensorId`, `leakDetected`, `location`, `severity`, `phoneNumber`, and `timestamp`.
- **Webhook Path:** `/smart-water-sensor`

---

### 2. Filter Leak Events
- **Type:** Function
- **Purpose:** Filters incoming webhook data to process only those events where a water leak has been detected (`leakDetected` field is `true`).
- **Logic:** Returns items only if a leak is detected, otherwise stops workflow for that item.

---

### 3. Log Leak Data
- **Type:** MongoDB Node
- **Purpose:** Logs each leak event into the `WaterLeakLogs` collection in MongoDB.
- **Data Stored:** 
  - `sensorId`
  - `leakDetected`
  - `timestamp` (current ISO timestamp)
  - `location` (defaults to `"unknown"` if not provided)
  - `severity` (defaults to `"normal"` if not provided)
- **Credentials Required:** MongoDB connection credentials.

---

### 4. Send SMS Alert
- **Type:** Twilio Node
- **Purpose:** Sends an SMS alert to the configured phone number in the sensor data.
- **Message:** 
  ```
  Alert: Water leak detected at {location or "your home"} sensor {sensorId} at {timestamp}. Please check immediately.
  ```
- **Credentials Required:** Twilio API credentials.

---

### 5. Send Email Alert
- **Type:** Email Send Node (SMTP)
- **Purpose:** Sends an email notification to the alert email address.
- **Email Details:**
  - **From:** smarthome@domain.com
  - **To:** alerts@domain.com
  - **Subject:** Water Leak Alert - Smart Home
  - **Body:** Detailed message including sensor ID, location, timestamp, and severity.
- **Credentials Required:** SMTP email credentials.

---

### 6. Trigger Water Shut-off Valve
- **Type:** HTTP Request Node
- **Purpose:** Sends a command to the water valve controller to close the valve when a leak is detected.
- **Request Parameters:**
  - `deviceId`: the sensor ID
  - `command`: `"close_valve"`
- **Credentials Required:** HTTP basic auth credentials for valve controller API.

---

### 7. Fetch Leak History
- **Type:** MongoDB Node
- **Purpose:** Retrieves the latest 100 leak events from the `WaterLeakLogs` collection, sorted by newest timestamp.

---

### 8. Analyze Leak Data
- **Type:** Function
- **Purpose:** Aggregates leak log data by date, counting leak events per day.
- **Output:** An array where each item represents a day with the corresponding leak count.

---

### 9. Generate Analytics Dashboard Data
- **Type:** HTML Node
- **Purpose:** Generates data in HTML format suitable for rendering a visual analytics dashboard.
- **Content:** Title `"Water Leak Analytics"` and daily leak count statistics.

---

## Workflow Connections

- Incoming sensor data to **Filter Leak Events**.
- Filtered leak events branch out to:
  - **Log Leak Data**
  - **Send SMS Alert**
  - **Send Email Alert**
  - **Trigger Water Shut-off Valve**
- Separate path for fetching and analyzing leak history:
  - **Fetch Leak History** → **Analyze Leak Data** → **Generate Analytics Dashboard Data**

---

## Credentials Setup

To run this workflow, ensure the following credentials are properly configured in your n8n instance:

- **MongoDB Credentials:** Access to the MongoDB database with a collection named `WaterLeakLogs`.
- **Twilio API Credentials:** For sending SMS alerts.
- **SMTP Email Credentials:** For sending email alerts.
- **Water Valve HTTP Credentials:** HTTP basic auth credentials for commanding the shut-off valve controller API.

---

## Usage

1. Deploy sensors and configure them to send HTTP POST requests to the webhook endpoint `/smart-water-sensor`.
2. Ensure credentials for MongoDB, Twilio, SMTP, and valve control API are set in n8n.
3. Activate the workflow.
4. On leak detection:
   - The event is logged,
   - SMS and email alerts are sent,
   - The water shut-off valve is triggered automatically.
5. View leak history and analytics from the generated dashboard to monitor patterns over time.

---

## Notes

- Sensor data must include necessary fields like `sensorId`, `leakDetected`, `location`, `phoneNumber` for alerts, and optionally `severity`.
- Timestamp is recorded based on the system time when the event is logged.
- The workflow is designed to act immediately on leak detection to reduce water damage risk.

---

*End of Documentation*