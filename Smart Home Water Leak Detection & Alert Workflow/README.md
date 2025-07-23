# Smart Home Water Usage Monitor and Leak Alert System

This workflow monitors water usage data from a smart home device, analyzes the data to detect potential leaks, sends alerts via email and SMS when unusual consumption is detected, and logs the water usage data daily.

---

## Workflow Overview

1. **Daily Trigger:** Executes the workflow every day at 9:00 AM.
2. **Fetch Water Usage Data:** Retrieves the last water usage data from the smart home API.
3. **Analyze Water Usage for Leak:** Processes the fetched data to calculate the average consumption over the past 7 days, compares the latest consumption against this average, and determines if a leak alert should be triggered.
4. **Send Email Alert:** Sends an email notification if a possible leak is detected or reports normal consumption.
5. **Send SMS Alert:** Sends an SMS notification with the same message as the email, using Twilio.
6. **Log Water Usage Data:** Appends the analyzed data to a local JSON log file for record-keeping.

---

## Nodes Description

### 1. Daily Trigger
- **Type:** Cron Trigger
- **Schedule:** Daily at 09:00 AM
- **Purpose:** Initiates the workflow every day to perform water usage monitoring.

### 2. Fetch Water Usage Data
- **Type:** HTTP Request
- **API Endpoint:** `https://api.smarthome/devices/water-usage`
- **Authorization:** Bearer token (`YOUR_API_TOKEN`)
- **Response Format:** JSON
- **Purpose:** Retrieves recent water consumption data required for leak analysis.

### 3. Analyze Water Usage for Leak
- **Type:** Function
- **Logic:**
  - Extracts water usage entries from the last 7 days.
  - Calculates the average daily consumption.
  - Finds the latest water consumption measurement.
  - Compares the latest reading to average multiplied by a threshold (2.5x).
  - Determines if a possible leak exists.
  - Returns an alert flag and message.
- **Threshold Multiplier:** 2.5 times average consumption.

### 4. Send Email Alert
- **Type:** Email Send
- **From:** `alerts@smarthome.com`
- **To:** User email from workflow data or default `homeowner@example.com`
- **Subject:** `Water Leak Alert: Smart Home Monitor`
- **Body:** Message from leak analysis node.
- **Purpose:** Notifies the user by email about potential leaks or normal status.

### 5. Send SMS Alert
- **Type:** Twilio Node
- **To:** User phone number from workflow data or default `+1234567890`
- **Message:** Same leak alert message as email.
- **Credentials:** `Twilio API` (replace with your Twilio credentials)
- **Purpose:** Provides SMS notifications for quick awareness.

### 6. Log Water Usage Data
- **Type:** Write Binary File
- **File:** `water-usage-logs.json` (appends data)
- **Purpose:** Keeps a persistent daily log of analyzed water usage data and alert status.

---

## Setup Instructions

1. **API Token:** Replace `YOUR_API_TOKEN` in the HTTP Request node with your actual API access token.
2. **Email Configuration:**
   - Set your sender email in the Email Send node (`alerts@smarthome.com`).
   - Update the default recipient email or ensure the userEmail field is available in input data.
3. **Twilio Credentials:**
   - Add your Twilio account credentials via n8n Credentials and link them in the Twilio node.
   - Update the default phone number if needed.
4. **File Permissions:**
   - Ensure the workflow has permissions to write to `water-usage-logs.json` in the defined directory.
5. **Scheduling:**
   - Adjust the Cron Trigger schedule if a different monitoring interval is desired.

---

## Data Flow Summary

- The workflow runs daily at 9 AM.
- Water usage data is fetched from the smart home API.
- Leak analysis evaluates if current consumption indicates a leak.
- Alerts are sent through Email and SMS when leak conditions are met.
- All data and alerts are logged locally for tracking history.

---

## Notes

- Customize threshold multiplier in the Analyze node to adjust leak sensitivity.
- Make sure user contact information (`userEmail`, `userPhone`) is either embedded in incoming data or defaults are appropriate.
- Monitor the log file `water-usage-logs.json` regularly for historical consumption trends and diagnostics.
- Ensure your smart home API endpoint and authentication remain valid for uninterrupted data fetching.

---

This workflow provides an automated and proactive approach to detect water leaks early and prevent potential water damage by timely user notification and detailed usage logging.