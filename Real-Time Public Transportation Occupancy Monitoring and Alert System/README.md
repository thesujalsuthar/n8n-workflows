# Real-Time Public Transportation Occupancy and Crowd Management System

## Overview

This workflow is designed to monitor real-time occupancy data from public transportation vehicles such as buses and trains. It processes sensor data to detect overcrowding, generates alerts, and distributes notifications via SMS, email, and social media platforms to manage crowd flow and inform passengers and authorities promptly.

---

## Workflow Nodes and Functionality

### 1. Receive Sensor Data

- **Type:** HTTP Trigger
- **Method:** GET
- **Endpoint:** `/sensor-data`
- **Function:** Listens for incoming real-time passenger count data from vehicle sensors.
- **Data Incoming:** JSON array including:
  - `vehicleId` — Unique identifier of the vehicle
  - `timestamp` — Time of data capture
  - `occupancyCount` — Current number of passengers

---

### 2. Preprocess Sensor Data

- **Type:** Function
- **Purpose:** Aggregates and preprocesses incoming sensor data.
- **Operation Details:**
  - Groups data points by `vehicleId`.
  - Sorts data by timestamp for each vehicle.
  - Extracts the latest occupancy count per vehicle.
- **Output:** Latest sensor record per vehicle in JSON format.

---

### 3. Analyze & Predict Overcrowding

- **Type:** Function
- **Purpose:** Analyze occupancy levels and predict overcrowding risks.
- **Details:**
  - Uses predefined maximum capacities per vehicle:
    - `bus1001`: 60 passengers
    - `bus1002`: 60 passengers
    - `train2001`: 200 passengers
    - `train2002`: 150 passengers
  - Default capacity is 100 if vehicle not listed.
  - Overcrowding threshold: 90% of max capacity.
  - Generates an alert if occupancy exceeds or is predicted to exceed this threshold.
- **Output:** Alert objects containing:
  - `vehicleId`
  - `occupancyCount`
  - `capacity`
  - `occupancyRate`
  - `alertType` (e.g., `OVERLOAD`)
  - Alert message string

---

### 4. Split Alerts

- **Type:** Split In Batches
- **Purpose:** Splits received alerts so each alert can be processed individually for notification delivery.

---

### 5. Send SMS Alert

- **Type:** Twilio Node
- **Purpose:** Sends SMS alert messages regarding overcrowding.
- **Destination:** Phone number retrieved from alert data or defaults to `+1234567890`.
- **Message:** Contains alert message about the overcrowding status.

**Note:** Requires Twilio API credentials setup.

---

### 6. Send Email Alert

- **Type:** Email Send
- **Purpose:** Emails overcrowding alerts to the transit authority.
- **Recipient:** `alerts@transitauthority.example.com`
- **Subject:** Overcrowding alert specifying the vehicle ID.
- **Content:** Alert message describing the situation.

**Note:** Requires SMTP email credentials setup.

---

### 7. Post Social Media Alert

- **Type:** Twitter Node
- **Purpose:** Publishes alerts on social media to inform the public quickly.
- **Message Format:** `"Alert: Vehicle <vehicleId> is currently overcrowded. Please expect delays and consider alternative options."`

**Note:** Requires Twitter OAuth2 API credentials setup.

---

## Workflow Data Flow

```
Receive Sensor Data --> Preprocess Sensor Data --> Analyze & Predict Overcrowding --> Split Alerts --> [Send SMS Alert, Send Email Alert, Post Social Media Alert]
```

- Starts by receiving live sensor data.
- Processes and aggregates data by vehicles.
- Analyzes for overcrowding conditions.
- Splits alerts to send to different communication channels.
- Dispatches notifications via SMS, email, and social media simultaneously.

---

## Prerequisites

- **n8n Instance:** Properly configured and running.
- **APIs and Credentials:**
  - Twilio account for SMS sending.
  - SMTP server credentials for email alerts.
  - Twitter developer credentials for posting tweets.
- **Sensor Data Source:** Devices or services sending passenger count data in the expected format.

---

## Setup Instructions

1. Deploy the workflow in your n8n instance.
2. Set up HTTP trigger endpoint `/sensor-data` to receive sensor data POST or GET requests.
3. Configure the Twilio node with your account credentials.
4. Set the SMTP credentials for the email node.
5. Configure Twitter OAuth2 credentials for social media alerts.
6. Adjust `maxCapacities` in the "Analyze & Predict Overcrowding" node as per actual vehicle capacities.
7. Activate the workflow.

---

## Alert Example

**SMS/Email/Twitter Message:**

```
Vehicle bus1001 is overcrowded with occupancy at 92%.
Alert: Vehicle bus1001 is currently overcrowded. Please expect delays and consider alternative options.
```

---

## Notes

- The default overcrowding threshold is 90% of maximum capacity.
- Vehicle IDs and capacities can be extended or modified in the Analyze & Predict node.
- Ensure real-time data feeds are reliable for timely alerts.
- Consider securing the HTTP endpoint to allow only trusted sources.

---

This system helps transit authorities efficiently monitor vehicle occupancy and inform passengers proactively to improve safety and comfort.