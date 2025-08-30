# SmartHome AirGuard Workflow

## Overview

The SmartHome AirGuard workflow is designed to monitor indoor air quality by periodically fetching data from an IoT air quality sensor API, analyzing the data for potential air quality issues, sending alerts when thresholds are exceeded, activating air purifiers, and logging data for historical analysis. It also sends a daily summary report of the air quality status.

---

## Workflow Components

### 1. Get Air Quality Data

- **Node Type:** HTTP Request
- **Description:** Polls the IoT sensor API every 10 seconds to retrieve the latest air quality data.
- **Endpoint:** `http://your-iot-sensor-api.local/air-quality`
- **Response Format:** JSON
- **Polling Interval:** 10,000 milliseconds (10 seconds)

---

### 2. Analyze Air Quality (AI)

- **Node Type:** Function
- **Description:** Analyzes sensor data for harmful pollutants and environmental conditions using predefined thresholds.
- **Thresholds:**
  - PM2.5 (`pm25`): 35 µg/m³
  - CO2 (`co2`): 1000 ppm
  - VOC (`voc`): 500 ppb
  - Humidity: 30% (low) - 60% (high)
  - Temperature: 18°C (low) - 28°C (high)
- **Output:**
  - Original sensor data
  - List of active alerts based on the thresholds
  - Count of alerts
  - Current timestamp

---

### 3. Check For Alerts

- **Node Type:** If
- **Description:** Checks whether any air quality alerts have been generated.
- **Condition:** `alertCount > 0`
- **Branches:**
  - **True:** Proceed to send alerts and activate the air purifier.
  - **False:** No alert branch (no operation).

---

### 4. Send Email Alert

- **Node Type:** Email Send
- **Description:** Sends an air quality alert email to the occupant if issues are detected.
- **From:** `airguard@example.com`
- **To:** Occupant’s email (`occupantEmail` from data or default `occupant@example.com`)
- **Subject:** `Indoor Air Quality Alert`
- **Message:** Lists all detected air quality issues with instructions to take precautions.

---

### 5. Send SMS Alert

- **Node Type:** Twilio (SMS)
- **Description:** Sends an SMS alert with detected air quality issues.
- **To:** Occupant’s phone number (`occupantPhone` from data or default `+1234567890`)
- **Message:** Concise list of alerts separated by semicolons.

---

### 6. Activate Air Purifier

- **Node Type:** HTTP Request
- **Description:** Sends a POST request to activate the home air purifier when alerts are detected.
- **Endpoint:** `http://your-home-automation.local/devices/air-purifier/activate`
- **Method:** POST
- **Body:** Empty JSON `{}`

---

### 7. Log Data to Google Sheets

- **Node Type:** Google Sheets Append
- **Description:** Logs each air quality reading and alerts to a Google Sheet for record-keeping.
- **Spreadsheet:** Identified by `Your_Google_Sheet_ID`
- **Sheet & Range:** `AirQuality!A:E`
- **Columns Logged:**
  - `timestamp`
  - `pm25`
  - `co2`
  - `voc`
  - `alerts`
- **Input Mode:** USER_ENTERED

---

### 8. Daily Schedule Trigger

- **Node Type:** Cron
- **Description:** Triggers the generation of daily air quality summary reports.
- **Schedule:** Every day at 08:00 AM

---

### 9. Get Historical Data

- **Node Type:** Google Sheets Get All
- **Description:** Retrieves all historical air quality data logged in the Google Sheet.
- **Spreadsheet:** `Your_Google_Sheet_ID`
- **Range:** `AirQuality!A:E`

---

### 10. Analyze Historical Data

- **Node Type:** Function
- **Description:** Computes average values of PM2.5, CO2, and VOC from the historical data.
- **Outputs Summary:**
  - Average PM2.5 (µg/m³)
  - Average CO2 (ppm)
  - Average VOC (ppb)
  - Number of data points used

---

### 11. Send Daily Report Email

- **Node Type:** Email Send
- **Description:** Sends the daily summarized air quality report to the occupant.
- **From:** `airguard@example.com`
- **To:** `occupant@example.com`
- **Subject:** `Daily Indoor Air Quality Report`
- **Message:** Contains averages of key air quality parameters and data points count.

---

### 12. No Alert Branch

- **Node Type:** NoOp (No Operation)
- **Description:** Placeholder node that does nothing when no alerts are detected.

---

## Workflow Execution Flow

1. **Continuous Monitoring:**
   - The workflow polls air quality data every 10 seconds.
   - Data is analyzed immediately after retrieval.
   - Alerts are checked; if any exist, emails and SMS are sent, and the air purifier is activated.
   - All data and alerts are logged into Google Sheets.

2. **Daily Reporting:**
   - At 8 AM daily, a scheduled trigger initiates loading all historical data.
   - Historical data is analyzed to compute average pollutant levels.
   - A summary report with these averages is emailed to the occupant.

3. **Error Handling & Defaults:**
   - In case occupant contact details are missing in sensor data, default email and phone numbers are used.
   - If no alerts are detected, the workflow ends with the No Alert Branch doing nothing.

---

## Prerequisites

- IoT Air Quality Sensor API accessible at the specified URL.
- Home Automation API endpoint for air purifier activation.
- Google Sheets with appropriate permissions and the sheet `AirQuality` prepared with columns for timestamp, PM2.5, CO2, VOC, and alerts.
- Email SMTP credentials configured in n8n for sending emails.
- Twilio account and credentials configured in n8n for sending SMS.

---

## Configuration

- Replace placeholder URLs (`your-iot-sensor-api.local` and `your-home-automation.local`) with actual endpoints.
- Replace `Your_Google_Sheet_ID` with your actual Google Sheet ID.
- Update email addresses and phone numbers as per occupant contact details.
- Adjust thresholds in the Analyze Air Quality (AI) node if needed to match desired alert criteria.

---

## Notes

- The polling interval can be adjusted based on sensor update frequency and system requirements.
- The daily report time can be modified in the Cron node.
- Alerts and data logging are decoupled, ensuring that even if alerting fails, data is recorded for future analysis.
- This workflow uses basic threshold analysis; advanced Machine Learning or AI integration can complement or replace the current function node logic for enhanced insights.