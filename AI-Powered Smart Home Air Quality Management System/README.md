# AI-Enhanced Smart Home Air Quality Monitoring & Alert System

This workflow automates the monitoring, analysis, and reporting of indoor air quality using sensor data fed into an AI model. It triggers alerts and activates your smart air purifier when poor air quality is detected. Additionally, it generates and emails a detailed daily air quality report summarizing the last 24 hours of sensor data.

---

## Workflow Overview

### 1. Real-Time Air Quality Monitoring & Alerting

- **Air Quality Sensor Trigger**  
  Listens to real-time air quality data (`pm25`, `pm10`, `co2`, `voc`, `temperature`, `humidity`) from an air quality sensor device via webhook.

- **Clean Sensor Data**  
  Sanitizes incoming sensor data by ensuring all parameters have numeric values, replacing any missing data with zero for consistency.

- **AI Analyze Air Quality**  
  Sends cleaned sensor data to OpenAI's `text-davinci-003` model with a prompt to:  
  - Analyze the indoor air quality.  
  - Identify pollutants and hazards.  
  - Assess health risk level (Low, Moderate, High).  
  - Provide actionable recommendations for improvement.  
  The AI response is expected in JSON format.

- **Parse AI Analysis**  
  Parses the AI's JSON response for further processing.

- **Check Risk Level**  
  Determines if the risk level is "Moderate" or "High". Only then does it proceed to alert actions.

- **Send Alert Email**  
  Sends an email alert to the user with the risk summary and detailed recommendations.

- **Trigger Air Purifier**  
  Sends an authenticated HTTP request to turn on the smart air purifier device to automatically improve indoor air quality when risk is elevated.

---

### 2. Daily Air Quality Reporting

- **Daily Schedule Trigger**  
  Runs once daily at 8:00 AM, initiating the reporting process.

- **Start Report Generation**  
  Marks the start of the daily report generation with the current date and time.

- **Fetch Last 24h Air Quality Data**  
  Retrieves the last 24 hours of air quality sensor readings from your smart home API.

- **Calculate Averages**  
  Computes average values over the last 24 hours for PM2.5, PM10, CO2, VOC, temperature, and humidity to summarize air quality trends.

- **Generate Air Quality Report**  
  Uses OpenAI `text-davinci-003` model to produce a detailed markdown report based on averaged sensor data. The report includes:  
  - Overall air quality assessment  
  - Potential issues and concerns  
  - Specific recommendations for improving indoor environment

- **Send Daily Report Email**  
  Emails the formatted markdown report to the user.

---

## Nodes Description

| Node Name                  | Purpose                                                                                      |
|----------------------------|----------------------------------------------------------------------------------------------|
| Air Quality Sensor Trigger | Webhook node receives real-time air quality sensor data                                      |
| Clean Sensor Data          | Cleans and structures raw sensor input data                                                 |
| AI Analyze Air Quality      | Sends sensor data to AI for risk and recommendation analysis                                 |
| Parse AI Analysis           | Parses AI JSON output                                                                        |
| Check Risk Level            | Conditional check on AI risk level (Moderate/High)                                          |
| Send Alert Email            | Sends alert email if risk level is elevated                                                 |
| Trigger Air Purifier        | Calls API to turn on air purifier automatically                                             |
| Daily Schedule Trigger      | Scheduled node triggers daily report generation                                             |
| Start Report Generation     | Prepares report generation context (timestamp)                                              |
| Fetch Last 24h Air Quality Data | Retrieves last 24 hours air quality data from API                                       |
| Calculate Averages          | Averages air quality data points over 24 hours                                              |
| Generate Air Quality Report | Generates detailed air quality report using AI                                              |
| Send Daily Report Email     | Sends daily air quality report via email                                                    |

---

## Configuration & Setup

### Required Credentials

- **Smart Home API Credentials (`smartHomeAPI`)**  
  Used in HTTP Request nodes to authenticate requests for sensor data and air purifier commands.

- **OpenAI API Key**  
  Required for AI nodes using the OpenAI integration. Ensure it’s set up in n8n’s credentials.

- **Email SMTP Credentials**  
  Configured in n8n for sending emails via the EmailSend nodes.

### Important Parameters to Update

- Replace `"your-sensor-device-id"` in the **Air Quality Sensor Trigger** with your actual indoor air quality sensor’s device ID.

- Replace `"your-purifier-device-id"` in the **Trigger Air Purifier** node with your smart air purifier’s device ID.

- Update the `"toEmail"` fields in both **Send Alert Email** and **Send Daily Report Email** nodes with your recipient email address.

- Update the API endpoint URL in **Fetch Last 24h Air Quality Data** node (`https://api.yoursmarthome.com/sensors/airQuality/history?last=24h`) to match your smart home system’s API.

---

## How It Works

### Real-Time Monitoring Flow

1. The **Air Quality Sensor Trigger** listens for new sensor data events.
2. Incoming data is cleaned for consistency.
3. The cleaned data is sent to OpenAI AI for detailed analysis.
4. Parsed AI analysis determines risk level.
5. If risk is moderate or high:
   - Alert email is sent with risk summary and recommendations.
   - Smart air purifier is activated to improve air quality.

### Daily Reporting Flow

1. Daily trigger fires at 8:00 AM.
2. The last 24 hours of sensor data is fetched from the smart home API.
3. Data averages are calculated.
4. AI generates a comprehensive air quality report.
5. Report is sent via email as a markdown-formatted summary.

---

## Benefits

- **Proactive Air Quality Management:** Receive timely alerts to act on poor indoor air conditions.
- **Automated Remediation:** Smart purifier activates automatically based on AI assessment.
- **Insightful Reporting:** Daily detailed reports help track trends and maintain a healthy environment.
- **AI-Driven Recommendations:** Leverages AI to interpret complex sensor data into actionable steps.

---

## Notes

- Ensure your smart home API supports required endpoints and authentication for device control and data retrieval.
- AI responses are based on OpenAI’s language model and should be reviewed periodically for accuracy and relevance.
- Adjust email addresses, device IDs, and API URLs according to your environment before activating the workflow.

---

## License

This workflow is provided as-is for personal and educational use. Modify and distribute according to your needs.