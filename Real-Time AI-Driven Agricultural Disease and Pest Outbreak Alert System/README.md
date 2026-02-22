# Real-Time AI Agriculture Disease and Pest Outbreak Prediction and Alert System

This workflow enables the detection and alerting of potential disease and pest outbreaks in agricultural fields using real-time data from multiple sources integrated with an AI prediction service. Alerts are sent via both email and SMS to farmers to facilitate timely action.

---

## Workflow Overview

The system collects, aggregates, and analyzes environmental and sensor data to predict crop disease and pest risks using an AI model. When risks exceed defined thresholds, alerts are automatically sent to farmers.

---

## Workflow Nodes Description

### 1. Get Weather Data

- **Type:** HTTP Request (GET)
- **Endpoint:** `https://api.openweathermap.org/data/2.5/weather`
- **Purpose:** Retrieves current weather data for the specified location.
- **Parameters:**
  - `q=YOUR_LOCATION` (Replace with location)
  - `appid=YOUR_API_KEY` (Your OpenWeatherMap API key)

### 2. Get Satellite Imagery Data

- **Type:** HTTP Request (GET)
- **Endpoint:** `https://api.satelliteimageryprovider.com/v1/imagery`
- **Purpose:** Fetches satellite imagery data for the location and current date.
- **Parameters:**
  - `location=YOUR_LOCATION` (Replace with location)
  - `date={{$now.format("YYYY-MM-DD")}}` (Dynamic date of execution)
  - `apikey=YOUR_API_KEY` (Your satellite imagery provider API key)

### 3. Get Local Sensor Data

- **Type:** HTTP Request (GET)
- **Endpoint:** `http://localsensors.api/fieldsensors/data`
- **Purpose:** Gathers local sensor data (e.g., soil moisture, temperature) from sensors installed in the agricultural field.
- **Parameters:**
  - `location=YOUR_LOCATION` (Replace with location)

### 4. Aggregate Data

- **Type:** Function
- **Purpose:** Combines data fetched from weather, satellite imagery, and local sensors into a single payload formatted for the AI prediction model.
- **Code Summary:**
  - Extracts relevant data from all previous requests.
  - Constructs an aggregated JSON object for prediction.

### 5. AI Prediction API

- **Type:** HTTP Request (POST)
- **Endpoint:** `https://api.yourmlservice.com/v1/predict`
- **Purpose:** Sends aggregated data to the AI API which predicts disease and pest risk levels.
- **Headers:**
  - `Authorization: Bearer YOUR_ML_API_KEY`
  - `Content-Type: application/json`
- **Body:**
  - JSON stringified aggregated data under the key `data`.

### 6. Evaluate Prediction and Create Alert

- **Type:** Function
- **Purpose:** Evaluates AI prediction results for disease and pest risk.
- **Logic:**
  - Checks if `diseaseRisk` or `pestRisk` exceeds 0.7 (70%).
  - If above threshold, generates an alert message including risk percentages and recommendations.
  - Outputs message along with farmer contact details.

### 7. Send Email Alert

- **Type:** Email Send
- **Purpose:** Sends an alert email to the farmer with the predicted risks and recommended actions.
- **Parameters:**
  - `From:` alerts@yourdomain.com
  - `To:` Dynamic, based on the farmer's email received in previous step
  - `Subject:` Crop Disease and Pest Outbreak Alert
  - `Text:` Alert message generated from prediction evaluation

### 8. Send SMS Alert

- **Type:** Twilio SMS Send
- **Purpose:** Sends an SMS alert to the farmer’s phone number containing the prediction alert message.
- **Parameters:**
  - `To:` Farmer's phone number (dynamic)
  - `Message:` Alert message generated from prediction evaluation

---

## Workflow Connection Flow

1. **Data Collection:**
   - *Get Weather Data*, *Get Satellite Imagery Data*, and *Get Local Sensor Data* nodes fetch data independently.
2. **Data Aggregation:**
   - All three data sets flow into *Aggregate Data* node to prepare the unified payload.
3. **AI Model Prediction:**
   - Payload sent to *AI Prediction API* node which returns disease and pest risk predictions.
4. **Risk Evaluation:**
   - *Evaluate Prediction and Create Alert* node processes predictions to determine if alerting is necessary.
5. **Alert Dispatch:**
   - Conditional alerts are sent out to farmers via *Send Email Alert* and *Send SMS Alert* nodes if risks are confirmed above threshold.

---

## Setup and Configuration

1. **Replace API Keys and Locations**
   - Insert your actual API keys in place of `YOUR_API_KEY` and `YOUR_ML_API_KEY`.
   - Specify the location in all nodes requiring the `YOUR_LOCATION` parameter.
2. **Farmer Contact Info**
   - Update the farmer's email and phone number in the *Evaluate Prediction and Create Alert* node as required.
3. **Email Configuration**
   - Configure email credentials and sender address for the *Send Email Alert* node.
4. **Twilio Setup**
   - Ensure Twilio account credentials and phone numbers are configured to enable SMS sending.

---

## Notes

- Thresholds for alerts (`>0.7`) can be adjusted in the *Evaluate Prediction and Create Alert* function node to fine-tune sensitivity.
- The AI prediction API endpoint and payload structure should comply with your machine learning service requirements.
- The workflow is designed to be extensible allowing additional sensor inputs or alert channels as needed.

---

This workflow facilitates proactive crop management by integrating environmental data and AI to mitigate agricultural risks effectively.