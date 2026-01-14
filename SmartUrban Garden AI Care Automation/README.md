# SmartUrban Garden AI Care Automation with Pest Detection and Eco-Friendly Treatment Scheduler

## Overview

This workflow automates the care of an urban garden by collecting sensor data, analyzing plant health and pest presence using AI, notifying the user, and scheduling eco-friendly treatment interventions. It integrates real-time sensor data ingestion, AI-driven analysis, user notifications, and calendar event scheduling for efficient garden management.

---

## Workflow Components

### 1. Sensor Data Webhook

- **Type:** Webhook (POST)
- **Purpose:** Receives real-time sensor data from the urban garden's sensors.
- **Endpoint:** `/smarturban-garden-sensor-data`
- **Data Expected:** JSON containing temperature, humidity, soil moisture, light level, optional pest image URL, and timestamp.

### 2. Parse Sensor Data

- **Type:** Function
- **Purpose:** Validates and structures incoming sensor data into a consistent format for analysis.
- **Outputs:**
  - `temperature`
  - `humidity`
  - `soilMoisture`
  - `lightLevel`
  - `pestImageUrl` (nullable)
  - `timestamp`

### 3. AI Plant Health & Pest Analysis

- **Type:** OpenAI (GPT-4)
- **Purpose:** Analyzes sensor data and optional pest image to detect plant health issues or pest presence.
- **Prompt Details:**
  - Inputs: Temperature (°C), Humidity (%), Soil Moisture (%), Light Level (lux), Pest Image URL.
  - Output JSON Keys:
    - `healthStatus`: One of `"Healthy"`, `"Stress Detected"`, or `"Pest Detected"`.
    - `issues`: Array of identified issues as strings.
    - `pestDetected`: Boolean flag for pest presence.
    - `recommendedTreatments`: Array of eco-friendly treatment suggestions.

### 4. Parse AI Analysis JSON

- **Type:** Function
- **Purpose:** Parses AI response from text to usable JSON format containing analysis results.

### 5. Conditional Check: If Pest Detected?

- **Type:** If
- **Purpose:** Routes the workflow based on the presence of pests detected in the analysis.
  - If `pestDetected` is `true`:
    - Notify user about pest detection.
    - Schedule treatment event.
  - Else (no pest detected):
    - Notify user with plant health status and stress updates.

### 6. Notify User Pest Detected

- **Type:** Email Send
- **Purpose:** Sends an email alerting the user to pest detection.
- **Details:**
  - From: `notifications@smarturban.garden`
  - To: `user@smarturban.garden`
  - Subject: "Pest Detected in Your Urban Garden"
  - Email body includes a list of recommended eco-friendly treatments.

### 7. Schedule Treatment Event

- **Type:** Google Calendar Event Creation
- **Purpose:** Creates a calendar event named "Eco-Friendly Treatment Intervention".
- **Details:**
  - Calendar ID: `ecoFriendlyGardening`
  - Description includes AI-recommended treatments.
  - Event Duration: Start now, end in 1 hour.
  - Reminder: Popup 15 minutes before event.

### 8. Notify User Healthy or Stress Update

- **Type:** Email Send
- **Purpose:** Sends an informative email about plant health status when no pests are detected.
- **Details:**
  - From: `notifications@smarturban.garden`
  - To: `user@smarturban.garden`
  - Subject: "Plant Health Status Update"
  - Email body lists current health status and any detected issues.

---

## Data Flow Summary

1. **Webhook receives sensor data.**
2. **Data is parsed and validated.**
3. **Parsed data sent to AI for health and pest analysis.**
4. **AI response is parsed into structured JSON.**
5. **If pests are detected:**
   - User is notified via email.
   - Treatment event is scheduled on Google Calendar.
6. **If no pests detected:**
   - User receives a plant health status update email.

---

## Requirements & Setup

- **n8n platform** with the following nodes configured:
  - Webhook (POST)
  - Function nodes for data parsing
  - OpenAI node (GPT-4 model) with API credentials
  - Google Calendar node with proper OAuth credentials and access to the `ecoFriendlyGardening` calendar
  - Email node configured with SMTP or basic authentication using the `notifications@smarturban.garden` sender address

- **Sensors** configured to send data to the defined webhook endpoint.

---

## Usage

- Deploy and activate the workflow in n8n.
- Connect your urban garden sensors to send POST requests with the correct data structure.
- Ensure OpenAI and Google Calendar credentials are properly set.
- Monitor your email for notifications about garden health and pest alerts.
- Check Google Calendar for scheduled eco-friendly treatment interventions.

---

## Notes

- Pest detection via image URL is integrated into the AI prompt but requires images to be accessible and relevant.
- Eco-friendly treatments recommended by AI focus on sustainable gardening practices.
- Email notifications support both plain text and HTML formatting for clarity.

---

## Contact

For support or questions regarding this workflow, contact the SmartUrban Garden development team at `support@smarturban.garden`.