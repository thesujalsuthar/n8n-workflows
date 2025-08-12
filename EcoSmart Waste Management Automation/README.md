# Eco-Friendly Smart Waste Management System

This workflow automates the monitoring and management of waste bins using sensor data to optimize pickup schedules and alert city services when bins require servicing.

---

## Overview

The system processes incoming sensor data from waste bins to determine fill levels, sends alerts for bins needing immediate service, schedules pickups, and integrates with Google Calendar to create events for the waste management providers. It also includes a health check endpoint to verify the workflow's operational status.

---

## Workflow Components

### 1. Webhook - Sensor Data Collector
- **Type:** HTTP POST Webhook
- **Path:** `/sensor-data`
- **Purpose:** Receives JSON payloads containing sensor data from waste bins.
- **Expected Input:**
  ```json
  {
    "data": [
      { "binId": "string", "fillLevel": number (0-100) },
      ...
    ]
  }
  ```
- **Response:** Returns processed alerts and pickup schedules as JSON.

### 2. Process Fill Levels (Function Node)
- **Role:** Analyzes sensor data to identify bins with fill levels at or above 75%.
- **Outputs:**
  - `alerts`: Array of bins requiring service.
  - `schedulePickups`: Array of bins scheduled for pickup (initially not scheduled).
- **Logic Summary:** 
  - Iterates sensor data.
  - If `fillLevel >= 75`, marks bin for alert and pickup scheduling.

### 3. Send Alert to City Services (Email Node)
- **Sends an email notification** to city services about bins needing urgent service.
- **From:** alerts@cityservices.example.com
- **To:** city.services@city.gov
- **Subject:** Waste Bin Alert: Bins Needing Service
- **Body:**
  Contains a list of all bins exceeding the threshold with their fill levels.
- **Credentials Required:** SMTP credentials (`SMTP_CityServices`).

### 4. Schedule Pickups (Function Node)
- **Purpose:** Creates pickup schedules for bins marked for servicing.
- **Details:**
  - Schedules pickups 2 hours from the current time.
  - Assigns "Local Waste Management" as the provider.
  - Sets pickup status as "scheduled".
- **Output:** Individual pickup schedule objects per bin.

### 5. Create Calendar Event (Pickup) (Google Calendar Node)
- **Function:** Adds pickup events to Google Calendar.
- **Details:**
  - Event summary includes Bin ID.
  - Event description explains automated scheduling.
  - Pickup event duration: 30 minutes starting from scheduled pickup time.
- **Credentials Required:** Google Calendar OAuth2 API credentials (`Google Calendar API Credential`).

### 6. Webhook - Health Check
- **Type:** HTTP GET Webhook
- **Path:** `/status`
- **Purpose:** Endpoint to verify system availability.
- **Response:** Returns JSON containing status `"OK"` and a timestamp.

### 7. Health Check Response (Function Node)
- **Provides:** A health status JSON response including current timestamp.

---

## Workflow Execution Flow

1. **Sensor Data Reception:**
   - Sensor devices POST data to `/sensor-data` webhook.
2. **Data Processing:**
   - The "Process Fill Levels" node analyzes data for critical bins.
3. **Alerts and Scheduling:**
   - If bins exceed the threshold, an alert email is sent.
   - Simultaneously, pickups are scheduled for those bins.
4. **Calendar Integration:**
   - For each scheduled pickup, corresponding calendar events are created automatically.
5. **Health Monitoring:**
   - The `/status` endpoint allows fast health checks, confirming the workflow is running correctly.

---

## Deployment and Configuration

- **Webhook URLs:** Replace `GENERATED_UNIQUE_ID_1` and `GENERATED_UNIQUE_ID_2` with your environment-specific webhook IDs or URLs.
- **SMTP Configuration:** Provide valid SMTP credentials under the name `SMTP_CityServices` to enable email alerts.
- **Google Calendar:** Set up Google OAuth2 credentials and store them as `Google Calendar API Credential` for calendar integration.
- **Sensor Data Format:** Ensure sensor data JSON follows the structure described for smooth processing.

---

## Example Sensor Data POST Request

```json
POST /sensor-data
Content-Type: application/json

{
  "data": [
    { "binId": "BIN123", "fillLevel": 80 },
    { "binId": "BIN456", "fillLevel": 60 },
    { "binId": "BIN789", "fillLevel": 90 }
  ]
}
```

---

## Response Example

```json
{
  "alerts": [
    { "binId": "BIN123", "fillLevel": 80, "needsService": true },
    { "binId": "BIN789", "fillLevel": 90, "needsService": true }
  ],
  "schedulePickups": [
    { "binId": "BIN123", "pickupScheduled": false },
    { "binId": "BIN789", "pickupScheduled": false }
  ]
}
```

---

## Health Check Example

- **Request:**
  ```
  GET /status
  ```
- **Response:**
  ```json
  {
    "status": "OK",
    "timestamp": "2024-06-01T12:34:56.789Z"
  }
  ```

---

## Summary

This workflow streamlines waste management by receiving live bin fill data, issuing alerts to city workers, scheduling pickups intelligently, and integrating with calendar systems, all while maintaining operational health visibility. It supports smarter, eco-friendly city services management.