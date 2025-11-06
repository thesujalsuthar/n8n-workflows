# AI-Driven Local Wildlife Monitoring and Conservation Alert System

## Overview

This workflow is designed to monitor local wildlife data in real-time using sensor inputs and trigger automated alerts for conservation teams. It fetches wildlife sensor data, processes it to detect critical conditions such as low animal population counts or distress signals, and sends notifications via email and Telegram to the relevant conservation authorities to enable prompt actions.

---

## Workflow Nodes Description

### 1. Fetch Wildlife Sensor Data

- **Type:** HTTP Request
- **Purpose:** Retrieves real-time data from local wildlife sensors.
- **URL:** `https://api.wildlifesensors.local/realtime-data`
- **Method:** GET
- **Headers:** Requires an API token via `Authorization: Bearer YOUR_API_TOKEN`.
- **Response Format:** JSON
- **Details:** This node pulls the latest data including animal counts, species, location, distress signals, and timestamps.

### 2. Process Conservation Alerts

- **Type:** Function
- **Purpose:** Analyzes fetched sensor data to identify potential alerts.
- **Function Logic:**
  - Iterates through each data item.
  - Triggers a "Low Population Count" alert if the animal count for a species falls below 5.
  - Triggers a "Distress Signal" alert if any distress signal is detected.
  - Gathers relevant information including alert type, species, location, animal count, and timestamp.
- **Output:** A list of alert objects to be forwarded for notifications.

### 3. Send Email Alert

- **Type:** Email Send
- **Purpose:** Sends detailed alert emails to the conservation team.
- **From:** `alerts@conservation.org`
- **To:** `conservation.team@organizations.local`
- **Subject:** `Wildlife Conservation Alert: {{$json["alertType"]}} detected`
- **Body:** Includes alert type, species, location, animal count (or 'N/A' if not applicable), and timestamp.
- **Note:** Urges recipients to take immediate conservation action.

### 4. Notify via Telegram

- **Type:** Telegram
- **Purpose:** Sends instant alert notifications to a designated wildlife conservation Telegram channel.
- **Chat ID:** `@wildlife_conservation_channel`
- **Message Content:** Formatted using Markdown to highlight alert type, species, location, animal count (or 'N/A'), and timestamp.
- **Notification:** Standard notification (not disabled).

---

## Workflow Execution Flow

1. **Fetch Wildlife Sensor Data:** The process starts by pulling live data from wildlife sensors.
2. **Process Conservation Alerts:** The data is analyzed to identify any critical conditions.
3. **Alerts Output:** If alerts are detected, they are simultaneously sent through email and Telegram notifications.
4. **Notification Recipients:** Conservation teams receive rapid updates to respond promptly.

---

## Setup Instructions

1. **API Authorization:**
   - Replace `YOUR_API_TOKEN` in the `Fetch Wildlife Sensor Data` node with your valid API token to access wildlife sensor data.

2. **Email Configuration:**
   - Ensure the email service credentials are configured in your n8n instance for the `Send Email Alert` node.
   - Update sender and recipient email addresses if necessary.

3. **Telegram Bot Setup:**
   - Configure the Telegram bot credentials in your n8n environment.
   - Confirm the `chatId` corresponds to the intended Telegram channel or group for receiving alerts.

4. **Activation:**
   - Activate the workflow in n8n to start continuous monitoring and alerting.

---

## Summary

This workflow provides an integrated solution for real-time wildlife monitoring and automated conservation alerting. It ensures that critical situations like population decline or distress are promptly communicated to conservation teams via multiple channels, enhancing response efficiency and supporting wildlife protection efforts.