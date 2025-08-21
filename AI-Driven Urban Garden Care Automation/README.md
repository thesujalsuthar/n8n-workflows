# AI-Powered Smart Urban Garden AI Care Automation

This workflow automates the monitoring and care of your urban garden using AI-driven analysis of sensor data. It collects environmental parameters, analyzes plant health using OpenAI's GPT-4, and provides actionable recommendations to optimize plant care. It also sends alerts and generates weekly reports to keep you informed.


## Workflow Overview

The workflow consists of two main cycles:

1. **Continuous Monitoring Cycle (every 5 minutes)**  
2. **Weekly Report Cycle (every Monday at 7 AM)**


---

## Nodes Detail

### 1. Sensor Data Collector Trigger  
- **Type:** Interval Trigger  
- **Function:** Triggers the workflow every 5 minutes (300 seconds) to collect sensor data.  
- **Position:** (250, 300)

### 2. Sensor Data Processor  
- **Type:** Function Node  
- **Function:** Simulates or fetches sensor data including:  
  - Soil Moisture (%)
  - Temperature (°C)
  - Light Level (lux)  
- **Note:** Replace simulation with actual sensor API or MQTT broker integration for real implementations.  
- **Position:** (500, 300)

```javascript
const soilMoisture = parseFloat((Math.random() * 100).toFixed(2));
const temperature = parseFloat((15 + Math.random() * 15).toFixed(2));
const lightLevel = parseFloat((Math.random() * 1000).toFixed(2));

return [{
  json: {
    soilMoisture,
    temperature,
    lightLevel,
    timestamp: new Date().toISOString()
  }
}];
```

### 3. AI Plant Health Analyzer  
- **Type:** HTTP Request Node  
- **Function:** Sends collected sensor data to OpenAI GPT-4 via Chat Completion API for analysis.  
- **Prompt:**  
  - Acts as an AI assistant specialized in plant health and smart garden automation.  
  - Provides plant health status and watering schedule optimization based on received data.  
- **API Authorization:** Uses OpenAI API key stored in credentials.  
- **Position:** (770, 300)

### 4. Extract AI Response  
- **Type:** Function Node  
- **Function:** Extracts the response content from OpenAI API's JSON response and prepares it for downstream nodes.  
- **Position:** (1000, 300)

```javascript
const aiResponse = $json.choices && $json.choices[0] && $json.choices[0].message && $json.choices[0].message.content ? $json.choices[0].message.content : "No response";

return [{ json: { aiAnalysis: aiResponse, timestamp: new Date().toISOString() } }];
```

### 5. Send Plant Care Alert  
- **Type:** Email Send Node  
- **Function:** Sends an email alert to the user with actionable plant care recommendations and the latest AI analysis.  
- **Email Details:**  
  - From: `no-reply@smartgarden.ai`  
  - To: Dynamic; defaults to `user@example.com` if no user email is provided  
  - Subject: `Urgent: Plant Care Alert - Tasks Recommended`  
  - Body includes AI analysis with timestamp in both text and HTML format.  
- **Position:** (1250, 220)

### 6. Save Weekly Plant Health Report  
- **Type:** Write Binary File Node  
- **Function:** Saves the AI analysis text into a file located in the folder `plant-health-reports`.  
- **Filename:** Includes date, e.g., `weekly-plant-health-report-YYYY-MM-DD.txt`  
- **Position:** (1250, 400)

---

### Weekly Report Cycle

#### 7. Weekly Report Trigger - Cron  
- **Type:** Cron Trigger  
- **Schedule:** Executes once every week on Monday at 7 AM.  
- **Position:** (250, 600)

#### 8. Aggregate Weekly Data for Report  
- **Type:** Function Node  
- **Function:** Aggregates all weekly sensor data and AI analyses into a summarized weekly report.  
- **Note:** Currently returns static placeholder content that should be replaced with actual aggregated data for real implementation.  
- **Position:** (500, 600)

```javascript
return [{
  json: {
    aiAnalysis: "Weekly Plant Health Report:\n- Soil moisture is within optimal range in 80% of measurements.\n- Temperature remained stable between 18-25°C.\n- Light levels optimal for photosynthesis.\n- Suggested watering schedule: every 4 days, adjust if rain detected."
  }
}];
```

#### 9. Send Weekly Report Email  
- **Type:** Email Send Node  
- **Function:** Sends a weekly summary report email to the user.  
- **Email Details:**  
  - From: `no-reply@smartgarden.ai`  
  - To: `user@example.com` (hardcoded, can be parameterized)  
  - Subject: `Weekly Plant Health Report`  
  - Body: Includes the weekly AI analysis report in plain text format wrapped in `<pre>` tags for formatting.  
- **Position:** (770, 600)


---

## Connections and Flow

- **Sensor Data Collection:**  
  `Sensor Data Collector Trigger` → `Sensor Data Processor` → `AI Plant Health Analyzer` → `Extract AI Response` →  
   ↳ `Send Plant Care Alert`  
   ↳ `Save Weekly Plant Health Report`

- **Weekly Report:**  
  `Weekly Report Trigger - Cron` → `Aggregate Weekly Data for Report` →  
  ↳ `Send Weekly Report Email`  
  ↳ `Save Weekly Plant Health Report`


---

## Setup & Configuration

1. **OpenAI API Key:**  
   Configure your OpenAI API key in the credentials under `openAIApi` for the HTTP Request node.

2. **Email SMTP Settings:**  
   Set up SMTP credentials for the Email Send nodes to enable sending email alerts and reports.

3. **Sensor Data Integration:**  
   Replace the simulated sensor data function with real data fetching logic from your sensor devices or IoT hub.

4. **File Storage:**  
   Ensure the workflow has access and write permissions to the `plant-health-reports` directory or adjust the path accordingly.

5. **Schedule Tuning:**  
   Modify the interval timer and cron schedule based on your monitoring and reporting needs.

---

## Summary

This workflow enables an AI-powered smart urban garden management system by:  
- Collecting environmental data from sensors every 5 minutes.  
- Analyzing plant health and care recommendations with GPT-4.  
- Sending immediate alerts to users for urgent tasks.  
- Aggregating weekly insights and distributing comprehensive reports.  

Leverage this automation to optimize plant care, conserve resources, and maintain a thriving urban garden effortlessly.