# Urban Wildlife AI Monitoring and Conservation Alert System

This workflow is designed to monitor urban wildlife and environmental conditions using IoT sensor data, analyze the data with AI to detect unusual events or potential threats, send alerts to relevant teams, and log the data for further review and record-keeping.

---

## Workflow Overview

1. **Schedule Trigger**  
   - Initiates the workflow at scheduled intervals to ensure continuous monitoring.

2. **Fetch IoT Sensor Data**  
   - Retrieves all data from connected IoT wildlife and environmental sensors, collecting information such as animal movements, environmental parameters, and other relevant metrics.

3. **Preprocess Sensor Data**  
   - Applies basic preprocessing steps on the raw sensor data such as normalization, noise filtering, or structuring to prepare it for AI analysis.

4. **AI Analyze Wildlife Data**  
   - Utilizes the GPT-4o-mini AI model to analyze the preprocessed sensor data.
   - Detects unusual patterns, anomalies, or potential threats like poaching, unexpected animal behavior, or hazardous environmental changes.
   - Returns a JSON-formatted output describing detected issues with confidence scores.

5. **Check For Alerts**  
   - Inspects the AI analysis results for the presence of issues that require attention.
   - If an alert-worthy status is detected, triggers alerting mechanisms.

6. **Send Alert Email**  
   - Sends an email notification to the conservation team and CCs local authorities.
   - The email contains detailed information about the detected unusual wildlife or environmental event.

7. **Send SMS Alert**  
   - Sends an SMS alert to a predefined phone number to ensure immediate awareness of the situation.

8. **Prepare Data for Logging**  
   - Structures the combined sensor data and AI analysis results together with a timestamp for logging purposes.

9. **Log Data to File**  
   - Appends the prepared data into a local JSON log file named `urban_wildlife_logs.json` for audit and future analysis.

---

## Nodes Description

### 1. Schedule Trigger
- Automatically triggers the workflow on a recurring schedule (configurable).
- Ensures timely collection and analysis.

### 2. Fetch IoT Sensor Data
- Connects to IoT devices to fetch all sensor data.
- Supports fetching data without filters to get complete sensor readings.

### 3. Preprocess Sensor Data
- A Function node that can be customized to preprocess the raw sensor data.
- Example preprocessing: data normalization, noise filtering, or reformatting.

### 4. AI Analyze Wildlife Data
- Calls OpenAI GPT-4o-mini model with a prompt tailored for wildlife and environmental monitoring.
- The prompt instructs the model to detect anomalies, threats, and unusual activities.
- Returns results strictly in JSON format indicating detected statuses and confidence.

### 5. Check For Alerts
- An If node that checks if the AI analysis has identified any issue (`status` field is not empty).
- Routes the flow to alert nodes or ends the alert process.

### 6. Send Alert Email
- Sends an urgent email alert to the conservation team and local authorities.
- Email includes details about the detected anomaly from the AI analysis results.

### 7. Send SMS Alert
- Sends an SMS notification to a designated phone number.
- Alerts recipients to check email for detailed information.

### 8. Prepare Data for Logging
- Formats sensor data and AI analysis output with a timestamp for logging.
- Ensures all relevant data is captured cohesively for records.

### 9. Log Data to File
- Appends the prepared data into a local JSON file (`urban_wildlife_logs.json`).
- Creates a persistent log for historical data and auditing.

---

## Setup and Configuration

1. **IoT Sensor Configuration**  
   Ensure your IoT wildlife and environmental sensors are registered and accessible via the "Fetch IoT Sensor Data" node.

2. **OpenAI API Setup**  
   - Configure the OpenAI API credentials in the "AI Analyze Wildlife Data" node.
   - Adjust model parameters or prompt if needed to tailor detection sensitivity.

3. **Email and SMS Alerts**  
   - Update recipient emails in the "Send Alert Email" node.
   - Replace the phone number in the "Send SMS Alert" node with the target alert recipient.
   - Configure SMTP or email sending credentials as required.
   - Configure Twilio or respective SMS service credentials.

4. **Scheduling**  
   - Adjust the trigger interval in the "Schedule Trigger" node to control how often the workflow runs.

5. **Logging**  
   - The log file `urban_wildlife_logs.json` will be created/updated in the execution environment.
   - Ensure write permissions exist for logging.

---

## Expected Output

- Regularly updated logs of sensor data and AI analysis.
- Email and SMS alerts only when unusual or potential threat events are detected.
- JSON payloads containing detection statuses with confidence scores.

---

## Notes

- The "Preprocess Sensor Data" function currently contains sample placeholder logic and should be customized based on the specific sensor data characteristics.
- Alert thresholds and conditions can be tuned via AI prompt and "Check For Alerts" logic.
- Secure handling of API keys and credentials is critical for deployment.

---

By integrating real-time IoT sensor data, AI-driven analysis, and automated alerting, this workflow enhances urban wildlife conservation efforts through timely and actionable monitoring.