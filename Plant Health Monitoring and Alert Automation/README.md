# Plant Health Monitoring Workflow

This workflow continuously monitors your plant's environmental conditions by fetching data from soil moisture, light, and temperature sensors, evaluates the health status based on predefined thresholds, and sends care reminders and environment alerts via Slack.

---

## Overview

- **Trigger:** Runs every 15 minutes.
- **Sensors:**
  - Soil Moisture Sensor
  - Light Sensor
  - Temperature Sensor
- **Processing:**
  - Aggregates sensor data.
  - Evaluates plant conditions against thresholds.
- **Notifications:**
  - Sends watering reminders if soil moisture is low.
  - Sends alerts for low/high light levels and temperature anomalies.
- **Communication:** Slack messages to a specified channel or user.

---

## Nodes and Workflow Details

### 1. Trigger Every 15 Minutes
- **Type:** Interval Trigger
- **Purpose:** Starts the workflow on a 15-minute schedule.

### 2. Soil Moisture Sensor
- **Type:** HTTP Request
- **Function:** Fetches soil moisture data.
- **Parameters:**
  - `deviceId`: Your soil moisture sensor device ID.
- **Authentication:** Basic Auth credentials for the smart sensor API.

### 3. Light Sensor
- **Type:** HTTP Request
- **Function:** Fetches light level data.
- **Parameters:**
  - `deviceId`: Your light sensor device ID.
- **Authentication:** Same Basic Auth credentials as soil moisture.

### 4. Temperature Sensor
- **Type:** HTTP Request
- **Function:** Fetches temperature data.
- **Parameters:**
  - `deviceId`: Your temperature sensor device ID.
- **Authentication:** Same Basic Auth credentials.

### 5. Aggregate Sensor Data
- **Type:** Set Node
- **Function:** Combines data from all three sensors into a single data object:
  - `soilMoisture`
  - `lightLevel`
  - `temperature`

### 6. Evaluate Plant Conditions
- **Type:** Function Node
- **Function:** Analyzes sensor readings against set thresholds and creates alerts and reminders.
- **Thresholds:**
  - Soil Moisture < 30% → watering reminder
  - Light Level < 200 lux → low light alert
  - Light Level > 10,000 lux → too much light alert
  - Temperature < 15°C → too cold alert
  - Temperature > 30°C → too hot alert
- **Output:**
  - `alerts`: array of alert messages.
  - `reminders`: array of care reminders.

### 7. Prepare Reminders Message
- **Type:** Set Node
- **Function:** Formats reminders into a single Slack message string.

### 8. Prepare Alerts Message
- **Type:** Set Node
- **Function:** Formats alerts into a single Slack message string.

### 9. Send Care Reminders
- **Type:** Slack Node
- **Function:** Sends the reminders message to the designated Slack channel or user.
- **Parameters:**
  - `channel`: Your Slack channel or user ID.
  - `text`: Populated from the reminders message.
- **Authentication:** Slack API credentials.

### 10. Send Environment Alerts
- **Type:** Slack Node
- **Function:** Sends the alerts message to the designated Slack channel or user.
- **Parameters:**
  - `channel`: Your Slack channel or user ID.
  - `text`: Populated from the alerts message.
- **Authentication:** Slack API credentials.

---

## Setup Instructions

1. **Configure Sensor Device IDs:**
   - Update the `deviceId` fields in the Soil Moisture Sensor, Light Sensor, and Temperature Sensor nodes with your actual device IDs.

2. **Configure Credentials:**
   - Set up HTTP Basic Auth credentials for your smart sensor API and select them in each HTTP request node.
   - Set up Slack API credentials and select them in both Slack nodes.

3. **Configure Slack Channel/User:**
   - Replace `"your-channel-or-user-id"` in both Slack nodes with the destination Slack channel or user ID to receive notifications.

4. **Adjust Thresholds (Optional):**
   - Modify the threshold values in the **Evaluate Plant Conditions** node function code to suit your plant’s specific needs.

5. **Activate Workflow:**
   - Ensure the workflow is enabled (`"active": true`) in your n8n instance.

---

## How It Works

- Every 15 minutes, the workflow triggers and requests current data from the soil moisture, light, and temperature sensors.
- The data from these sensors are collected and combined.
- The combined data is analyzed against predefined thresholds for plant health indicators.
- If thresholds are crossed, relevant reminders and alerts are generated.
- Reminders and alerts messages are sent to your Slack channel or user to prompt plant care actions or notify environmental anomalies.

---

## Notes

- Make sure your sensors are accessible through the specified API with proper authentication.
- The workflow is designed with a 15-minute polling interval, which can be adjusted as needed.
- Continue on fail (`continueOnFail: true`) is enabled for Slack nodes to avoid stopping the workflow if message sending fails.

---

Enhance your plant care routine by automating condition monitoring and real-time notifications with this workflow!