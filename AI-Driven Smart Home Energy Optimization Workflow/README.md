# AI-Powered Smart Home Energy Saver

This workflow leverages smart meter data and AI analysis to optimize energy consumption in a smart home environment. It identifies peak energy usage periods, recommends adjustments, executes those adjustments through smart devices, and notifies the user with a detailed consumption report.

---

## Workflow Overview

1. **Fetch Smart Meter Data**  
   Retrieves real-time energy consumption data from a smart meter API.

2. **Analyze Consumption with AI**  
   Analyzes the consumption data to identify peak consumption hours, calculate average usage, and recommend device adjustments.

3. **Prepare Device Commands**  
   Formats the AI-generated recommendations into actionable commands for smart home devices.

4. **Send Commands to Smart Devices**  
   Sends control commands to smart devices to apply energy-saving adjustments.

5. **Generate Alert Message**  
   Creates a summary report of the energy consumption and applied adjustments.

6. **Send Email Alert**  
   Sends the generated report to the user’s email.

---

## Node Details

### 1. Fetch Smart Meter Data  
- **Type:** HTTP Request (GET)  
- **Endpoint:** `https://api.smartmeter.example.com/v1/consumption/realtime`  
- **Function:** Fetches current energy consumption data including hourly usage.

### 2. Analyze Consumption with AI  
- **Type:** Function  
- **Logic:**  
  - Processes hourly consumption data.
  - Flags hours with consumption greater than 1.5 kWh as peak hours.
  - Recommends decreasing HVAC temperature by 2 degrees during peak hours.
  - Calculates average consumption across all hours.
- **Output:**  
  - `peakHours`: List of peak consumption hours.  
  - `averageConsumption`: Average kWh consumption.  
  - `recommendedAdjustments`: Array of device adjustment commands.

### 3. Prepare Device Commands  
- **Type:** Function  
- **Logic:**  
  - Extracts recommended adjustments from AI analysis.
  - Converts adjustments into command objects containing `deviceId`, `action`, and `value`.

### 4. Send Commands to Smart Devices  
- **Type:** HTTP Request (POST)  
- **Endpoint:** `https://api.smartdevices.example.com/v1/device/control`  
- **Function:** Sends each formatted command to the smart device API to apply the recommended settings.

### 5. Generate Alert Message  
- **Type:** Function  
- **Logic:**  
  - Constructs an energy consumption report string.
  - Includes average consumption, peak hours, and confirmation of adjustments applied.

### 6. Send Email Alert  
- **Type:** Email Send  
- **Parameters:**  
  - **To:** User email (dynamic variable `{{$userEmail}}`)  
  - **Subject:** "Smart Home Energy Consumption Report"  
  - **Message:** The alert message generated previously.

---

## How It Works

- The workflow starts by fetching current energy usage data via the smart meter API.
- The AI logic analyzes this data to detect inefficiencies and suggest ways to reduce consumption.
- Recommended device commands are generated and sent to smart home devices to effect changes, such as lowering HVAC temperature during peak hours.
- Finally, a report is generated and emailed to the user summarizing the consumption pattern and actions taken.

---

## Customization

- **API Endpoints:** Modify the URLs to match your smart meter and smart device APIs.
- **Thresholds & Recommendations:** Adjust consumption thresholds and device adjustment logic in the AI analysis node as needed.
- **Email Recipient:** The recipient email is set dynamically via `{{$userEmail}}` but can be hardcoded if preferred.

---

## Prerequisites

- Access credentials and API keys (if required) for the smart meter and smart device APIs.
- An email service configured within n8n for sending alerts.
- User email address available as a workflow variable `{{$userEmail}}`.

---

## Summary

This workflow automates smart home energy management by combining real-time data retrieval, AI-powered analysis, device control, and user notifications to optimize energy consumption and provide actionable insights.