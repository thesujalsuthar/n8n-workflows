# AI-Powered Real-Time Wildfire Risk Prediction and Community Alert System

This workflow provides an automated system to assess wildfire risk in real-time using multi-source environmental data combined with AI analysis, and subsequently informs communities via SMS, email, and social media alerts.

---

## Workflow Overview

The system integrates weather data, satellite imagery, and environmental sensor data to generate a comprehensive wildfire risk evaluation. An AI model analyzes the aggregated data, classifies the wildfire risk into one of four levels (LOW, MODERATE, HIGH, CRITICAL), and generates a brief explanation. Alerts are then dispatched to targeted recipients and community channels.

---

## Node-by-Node Breakdown

### 1. Get Real-Time Weather Data
- **Type:** HTTP Request  
- **Purpose:** Fetches current weather conditions such as temperature, humidity, wind speed, etc.  
- **Source:** Weather.com API  
- **Parameters:**  
  - Uses geolocation (`latitude`, `longitude`) from incoming data.  
  - Requires Weather API key credentials.  
- **Output:** JSON weather data.

---

### 2. Fetch Latest Satellite Imagery
- **Type:** AWS S3  
- **Purpose:** Retrieves the most recent satellite imagery related to wildfire activity or land conditions.  
- **Source:** AWS S3 bucket named `satellite-wildfire-data` within the `latest/` prefix.  
- **Credentials:** AWS S3 credentials required.  
- **Output:** JSON data representing the latest satellite imagery metadata or data.

---

### 3. Get Sensor Network Data
- **Type:** HTTP Request  
- **Purpose:** Collects environmental data such as temperature, humidity, smoke, or other sensor metrics captured in the last 10 minutes.  
- **Source:** Environmental sensor network API (`sensor-network.example.com`).  
- **Output:** JSON sensor data.

---

### 4. Aggregate Environmental Data
- **Type:** Function  
- **Purpose:** Merges data from weather, satellite imagery, and sensor sources into a unified JSON object to prepare as input for the AI assessment.  
- **Code Logic:** Combines payloads from the first three nodes under keys `weatherData`, `satelliteData`, and `sensorData`.

---

### 5. AI Wildfire Risk Assessment
- **Type:** OpenAI (AI Model)  
- **Purpose:** Analyzes all aggregated environmental data to estimate wildfire risk.  
- **Model:** `gpt-4o-mini`  
- **Input Prompt:**  
  - Receives combined environmental data.  
  - Responds with classified risk level: LOW, MODERATE, HIGH, or CRITICAL.  
  - Provides a brief explanation supporting the classification.  
- **Response Limits:** Up to 200 tokens; stops at newline.

---

### 6. Parse AI Risk Assessment
- **Type:** Function  
- **Purpose:** Extracts and formats the AI model's output for consumption by alerting systems.  
- **Functionality:**  
  - Parses AI response text to find risk level and explanation.  
  - Normalizes risk level string to uppercase (LOW, MODERATE, HIGH, CRITICAL).  
  - Constructs a clear alert message combining level and explanation.

---

### 7. Send SMS Alert
- **Type:** Twilio  
- **Purpose:** Sends the wildfire risk alert message via SMS to recipient phone numbers.  
- **Credentials:** Twilio API credentials required.  
- **Parameters:**  
  - Recipient phone number expected as part of the input data (`phoneNumber`).  
  - Alert message from parsed AI assessment.

---

### 8. Send Email Alert
- **Type:** Email Send  
- **Purpose:** Dispatches wildfire risk alerts via email to community or organizational mailing lists.  
- **Credentials:** SMTP mail server credentials required.  
- **Email Details:**  
  - Subject includes the risk level (e.g., "Wildfire Risk Alert: HIGH").  
  - Body contains the alert message.

---

### 9. Post Social Media Alert
- **Type:** Twitter  
- **Purpose:** Posts alert messages on Twitter to inform a wider community audience in real-time.  
- **Credentials:** Twitter OAuth2 credentials required.  
- **Content:** Text of the wildfire risk alert. Media URLs can be added if applicable.

---

## Data Flow and Connections

- The first three nodes (**Weather Data**, **Satellite Imagery**, **Sensor Data**) run in parallel and feed their outputs into **Aggregate Environmental Data**.
- The aggregated data is passed to the **AI Wildfire Risk Assessment** node for analysis.
- AI output is parsed by the **Parse AI Risk Assessment** function to extract actionable information.
- The parsed alert is simultaneously sent through **SMS**, **Email**, and **Twitter** alert nodes.

---

## Credentials and Configuration

To operate the workflow, the following credentials must be configured securely:

- **Weather API:** API key for weather.com access.  
- **AWS S3:** Permissions to read satellite imagery from the specified bucket.  
- **Twilio API:** For sending SMS messages.  
- **SMTP Email Server:** For sending email alerts.  
- **Twitter OAuth2:** For posting alerts on Twitter.

---

## Usage Notes

- Geolocation details (`latitude`, `longitude`) must be provided as input JSON to fetch localized weather data accurately.  
- Phone numbers for SMS sending should be included in the input data under `phoneNumber`.  
- Email recipients and Twitter posting accounts should be pre-configured within the respective nodes.

---

## Summary

This automated workflow combines real-time environmental monitoring with advanced AI prediction to proactively identify wildfire risks. It ensures timely communication to communities and stakeholders via multiple alert channels to support preparedness and response efforts effectively.