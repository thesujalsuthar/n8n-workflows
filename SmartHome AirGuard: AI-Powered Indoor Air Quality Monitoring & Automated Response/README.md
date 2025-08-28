# SmartHome AirGuard: AI-Driven Indoor Air Quality Monitoring & Alerts

## Overview
SmartHome AirGuard is a workflow designed to continuously monitor indoor air quality using data from smart air quality devices. It analyzes pollutant levels, issues alerts and recommendations when thresholds are exceeded, sends notifications via mobile and email, and automatically activates ventilation systems to improve air quality.

---

## Workflow Nodes

### 1. Fetch Indoor Air Quality Data
- **Type:** HTTP Request
- **Description:** Connects to the Smart Air Quality Devices API endpoint (`https://api.smartairqualitydevices.com/v1/air-quality`) to retrieve the latest air quality metrics including PM2.5, CO2, TVOC, and CO levels.
- **Authentication:** HTTP Basic Auth with `SmartAirQuality API Credentials`.

### 2. Analyze Air Quality
- **Type:** Function
- **Description:** Processes retrieved air quality data by comparing pollutant values against predefined safety thresholds:
  - PM2.5 > 35 µg/m³
  - CO2 > 1000 ppm
  - TVOC > 500 ppb
  - CO > 9 ppm
- Generates:
  - **Alerts**: List of pollutants exceeding safe levels with specific messages.
  - **Recommendations**: Actionable suggestions to mitigate poor air quality.
  - **Alert Level:** Tagged as `"warning"` if any thresholds are breached, otherwise `"normal"`.

### 3. Check for Alerts
- **Type:** If (Condition)
- **Description:** Evaluates if the alert level is `"warning"`.
  - If **true**, it triggers multiple subsequent actions (notifications and ventilation control).
  - If **false**, proceeds to the No Alerts node.

### 4. Send Mobile Notification
- **Type:** Pushbullet
- **Description:** Sends a push notification titled "SmartHome AirGuard Notification" with detailed alerts and recommended actions.
- **Priority:** High (1)
- **Authentication:** Pushbullet API credentials required.

### 5. Send Email Alert
- **Type:** Email Send
- **Description:** Sends an email with subject "SmartHome AirGuard Alert: Indoor Air Quality Warning" including the alert details and recommendations.
- **Recipient:** Defined recipient email address (e.g., `user@example.com`).
- **Authentication:** SMTP credentials required.

### 6. Turn On Ventilation
- **Type:** HTTP Request
- **Description:** Sends a command to the smart thermostat device to turn on ventilation with parameter `{ state: "on" }`.
- **Device:** Smart thermostat identified as `"smart-thermostat"`.
- **Authentication:** HTTP Basic Auth with `SmartThermostat API Credentials`.

### 7. No Alerts
- **Type:** No-Operation (NoOp)
- **Description:** Placeholder node to continue workflow silently when no alerts are present.

---

## Workflow Execution Flow

1. **Data Retrieval:** Start by fetching the latest indoor air quality data.
2. **Analysis:** Analyze data against safety thresholds and generate alerts/recommendations.
3. **Decision:** Check if any alerts were triggered.
   - If alerts exist:
     - Send a mobile push notification.
     - Send an email alert.
     - Activate ventilation system.
   - If no alerts:
     - Proceed with no further action.

---

## Credentials Setup

- **SmartAirQuality API Credentials:** HTTP Basic Auth credentials used to access air quality data API.
- **Pushbullet API:** API key/token for sending push notifications.
- **SMTP Credentials:** For sending email alerts via an SMTP server.
- **SmartThermostat API Credentials:** HTTP Basic Auth credentials for controlling the smart thermostat.

---

## Customization

- **Thresholds:** Threshold pollutant values can be updated in the `Analyze Air Quality` function node.
- **Notification Recipients:** Email address in the `Send Email Alert` node can be changed to the desired recipient.
- **Device Commands:** Ventilation device and command parameters can be adapted for different hardware.

---

## Summary

SmartHome AirGuard provides a robust, automated solution to ensure safe indoor air quality by:
- Continuously monitoring critical air pollutants.
- Providing real-time alerting through mobile and email channels.
- Initiating corrective ventilation actions to improve indoor environment.

This workflow empowers users to respond promptly to indoor air quality hazards, contributing to healthier living spaces.