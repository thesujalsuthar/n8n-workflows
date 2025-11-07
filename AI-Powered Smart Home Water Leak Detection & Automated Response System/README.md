# AI-Driven Smart Home Water Leak Detection & Alert System

This workflow implements an AI-powered solution to monitor water sensor data from a smart home, analyze it for potential leaks, and trigger immediate alerts and automated actions to prevent water damage. The system integrates IoT sensors, AI analytics, and notification services to provide a comprehensive leak detection and response mechanism.

---

## Workflow Overview

The workflow executes the following key steps:

1. **Fetch Water Sensor Data**  
   Polls real-time water sensor data from your IoT platform's REST API using authenticated HTTP requests.

2. **Prepare Data for AI**  
   Processes and formats the raw sensor data to match the input requirements of the AI leak detection API.

3. **AI Leak Detection**  
   Sends the prepared sensor data to an AI-powered API to analyze and determine if a leak is present.

4. **Evaluate AI Result**  
   Processes the AI response, continuing the workflow only if a leak is detected; otherwise, it stops further action.

5. **Send SMS Alert**  
   Sends an immediate SMS notification via Twilio to the homeowner alerting them of the detected leak.

6. **Send Email Alert**  
   Sends a detailed email alert to the homeowner with instructions and leak details for quick response.

7. **Trigger Water Shutoff Valve**  
   Sends a command to the home automation system to automatically shut off the main water supply to prevent damage.

---

## Node Details

### 1. Fetch Water Sensor Data

- **Type:** HTTP Request
- **Purpose:** Query IoT REST API to retrieve current water sensor data.
- **Parameters:**  
  - `deviceIds`: Array containing your water sensor device ID.  
  - Authenticated using HTTP Basic Auth credentials for secure access.
- **Notes:** Continuously polls the sensor for data such as moisture level and raw leak detection indicators.

### 2. Prepare Data for AI

- **Type:** Function
- **Purpose:** Extracts relevant sensor data and formats it to the AI API's expected input structure.
- **Details:**  
  - Extracts `data` from sensor response.
  - Returns a JSON object with `waterData` key holding sensor metrics.

### 3. AI Leak Detection

- **Type:** HTTP Request
- **Purpose:** Calls an external AI API endpoint to analyze the sensor data for signs of water leakage.
- **Parameters:**  
  - `URL`: Your AI leak detection API URL (e.g., `https://your-ai-leak-detection-api.com/analyze`).  
  - HTTP Method: POST  
  - Authentication: Bearer token sent in Authorization header.  
  - Request Body: JSON containing sensor data.  
  - Response Format: JSON

### 4. Evaluate AI Result

- **Type:** Function
- **Purpose:** Checks AI analysis response; proceeds only if a leak is detected.
- **Logic:**  
  - If `leakDetected` is `true`, triggers alert and automation actions.  
  - Otherwise, halts workflow by returning an empty array.

### 5. Send SMS Alert

- **Type:** Twilio
- **Purpose:** Notifies homeowner by SMS immediately upon leak detection.
- **Parameters:**  
  - `phoneNumber`: Homeowner's phone number (default or from data).  
  - `message`: Predefined alert message detailing the leak situation.
- **Credentials:** Configured Twilio API credentials.

### 6. Send Email Alert

- **Type:** Email Send
- **Purpose:** Sends an email alert with leak details and instructions to the homeowner.
- **Parameters:**  
  - `fromEmail`: Sender email address (e.g., no-reply@yourdomain.com).  
  - `toEmail`: Homeowner’s email address.  
  - `subject`: Clear subject line indicating water leak alert.  
  - `text`: Detailed message with instructions and notification about automated shutoff.
- **Credentials:** SMTP credentials for email sending.

### 7. Trigger Water Shutoff Valve

- **Type:** HTTP Request
- **Purpose:** Sends command to smart home automation system to turn off the water valve, preventing damage.
- **Parameters:**  
  - `device`: Water shutoff valve device ID.  
  - `command`: Action to execute, e.g., "turn_off".
- **Credentials:** HTTP Basic Auth credentials for home automation API.

---

## Workflow Connections

- The flow starts by fetching sensor data.
- Sensor data is processed and sent to AI Leak Detection.
- Based on AI analysis, the workflow either stops or triggers alerts and shutoff commands in parallel:
  - SMS alert to homeowner
  - Email alert to homeowner
  - Automated water shutoff command

---

## Prerequisites & Configuration

- **IoT Platform:**
  - Obtain your water sensor device ID.
  - Setup HTTP Basic Auth credentials for API access.

- **AI Leak Detection API:**
  - Access to AI endpoint URL.
  - API Key or Bearer token for authentication.

- **Notification Services:**
  - Twilio account and API credentials for SMS.
  - SMTP server credentials for sending emails.

- **Home Automation System:**
  - Device ID for the water shutoff valve.
  - API credentials for sending control commands (HTTP Basic Auth).

- **Workflow Parameters:**
  - Replace placeholder values such as device IDs, API URLs, phone numbers, email addresses, and credentials with your actual configuration.

---

## Summary

This workflow leverages AI and IoT integrations for smart, automated home water leak detection—providing real-time alerts and mitigation actions vital for preventing water damage. With the combined power of AI analytics, instant notifications, and automation, homeowners gain peace of mind and rapid response to minimize potential losses.