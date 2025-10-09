# SmartHome AI Energy Storage Optimizer

This workflow optimizes the management of a smart home's energy storage system using AI-powered forecasting and battery control. It collects real-time energy data, forecasts future energy consumption and production, decides optimal battery actions, controls the battery system, and sends status reports via email and Telegram.

---

## Workflow Overview

### 1. Receive Real-Time Energy Data
- **Node:** `Receive Real-Time Energy Data` (HTTP Trigger)
- **Details:**  
  Listens for incoming HTTP GET requests at the path `/energy-data` to receive real-time data from the smart home's energy sensors. The data includes:
  - Current energy consumption (kWh)
  - Current energy production (kWh)
  - Current battery charge level (%)

---

### 2. Parse Energy Data
- **Node:** `Parse Energy Data` (Function)
- **Details:**  
  Parses the incoming JSON payload to extract numeric values for consumption, production, and batteryLevel, ensuring the data is correctly formatted for downstream use.

---

### 3. Forecast Energy Usage
- **Node:** `Forecast Energy Usage` (Function)
- **Details:**  
  Simulates an AI forecast for the next 24 hours of energy consumption and production on an hourly basis.  
  - Generates simplistic sinusoidal models based on current consumption and production.
  - Creates a forecast array with hourly predictions, representing expected fluctuations.

---

### 4. Decide Battery Action
- **Node:** `Decide Battery Action` (Merge)
- **Details:**  
  Combines the forecast data with the current battery level to determine optimal battery actions:
  - Constants:
    - Battery capacity: 100 kWh
    - Minimum battery level threshold: 10%
    - Maximum battery level threshold: 90%
  - Evaluates net energy forecast (production minus consumption) over 24 hours.
  - If surplus energy is expected and battery is below max charge, instructs charging.
  - If deficit is expected and battery is above min level, instructs discharging.
  - Otherwise, holds current state.

Returns an object indicating:
- `action` (`charge`, `discharge`, or `hold`)
- `chargePower` (amount of power to charge or discharge)

---

### 5. Prepare Battery Control Payload
- **Node:** `Prepare Battery Control Payload` (Function)
- **Details:**  
  Prepares the payload based on battery action and charge power to be sent to the battery control API.

---

### 6. Control Battery System
- **Node:** `Control Battery System` (HTTP Request)
- **Details:**  
  Sends an HTTP POST request to the smart home's battery control API endpoint (`http://home-energy-mgmt.local/api/battery/control`) with JSON payload including:
  - `action`: battery operation command (`charge`, `discharge`, or `hold`)
  - `power`: power in kWh for charging or discharging

Headers specify `Content-Type: application/json`.

---

### 7. Create Status Report
- **Node:** `Create Status Report` (Function)
- **Details:**  
  Generates a human-readable summary report including:
  - Current consumption (kWh)
  - Current production (kWh)
  - Battery level (%)
  - Battery action taken
  - Power used for charging/discharging (kWh)

---

### 8. Send Email Report
- **Node:** `Send Email Report` (Email Send)
- **Details:**  
  Emails the daily energy storage status report to the user with:
  - From: `smarthome@domain.com`
  - To: `user@domain.com`
  - Subject: `Daily SmartHome Energy Storage Report`
  - Body: Text message generated in the previous step

---

### 9. Telegram Notification
- **Node:** `Telegram Notification` (HTTP Request)
- **Details:**  
  Sends the same status report as a Telegram message via Bot API to notify the user instantly.  
  Update `YOUR_BOT_TOKEN` in the webhook URL to your actual bot token.

---

## Flow Connections Summary

- Data flows from **Receive Real-Time Energy Data** → **Parse Energy Data**.
- Parsed data splits to both **Forecast Energy Usage** and one input of **Decide Battery Action**.
- The forecast output is connected to the other input of **Decide Battery Action**.
- The battery action decision feeds two branches:
  - Preparing and sending battery control commands.
  - Creating and distributing status reports via Email and Telegram.

---

## Requirements

- n8n automation platform
- External system sending real-time energy data via HTTP GET request to `/energy-data`
- Battery system API reachable at `http://home-energy-mgmt.local/api/battery/control`
- Valid SMTP credentials and email settings configured in n8n for sending email
- Telegram Bot token and chat configuration for notifications

---

## Configuration Notes

- Adjust battery parameters such as capacity, minimum, and maximum charge levels in the **Decide Battery Action** node to match your battery specifications.
- Replace email addresses in **Send Email Report** node with valid sender and recipient addresses.
- Replace `YOUR_BOT_TOKEN` with your actual Telegram bot token in the **Telegram Notification** node.
- Ensure that the external systems (smart home sensors, battery API) are reachable from the n8n instance.

---

## Usage

1. Deploy or activate the workflow in n8n.
2. Ensure the smart home's energy sensors POST to the `/energy-data` endpoint.
3. The workflow will automatically:
   - Parse real-time data.
   - Forecast usage.
   - Decide battery actions.
   - Control the battery system.
   - Send daily reports via email and Telegram.

This process helps optimize battery charge/discharge cycles to improve home energy efficiency and utilization of renewable energy sources.