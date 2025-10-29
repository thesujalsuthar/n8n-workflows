# SmartHome Energy Guardian

## Overview
The **SmartHome Energy Guardian** workflow is designed to monitor your home's energy consumption by integrating data from your smart meter, local weather forecasts, and regional energy pricing. It analyzes this data to detect unusual energy usage patterns, estimate your carbon footprint, and provide actionable recommendations. Alerts and daily reports are sent directly to the homeowner via Telegram for real-time updates and insights.

---

## Workflow Components

### 1. Get Smart Meter Data
- **Type:** HTTP Request
- **Description:** Fetches real-time energy usage data from the smart meter provider’s API.
- **Endpoint:** `https://api.smartmeterprovider.com/v1/energy-usage`
- **Authentication:** Bearer Token via `smartMeterApi` credentials
- **Output:** JSON containing current usage, average daily usage, and timestamp.

### 2. Get Weather Forecast
- **Type:** HTTP Request
- **Description:** Retrieves a 5-day weather forecast to understand environmental conditions affecting energy consumption.
- **Endpoint:** `https://api.weather.com/v3/wx/forecast/daily/5day`
- **Query Parameters:**
  - `geocode`: `"40.7128,-74.0060"` (Latitude and Longitude for New York City)
  - `format`: `"json"`
  - `apiKey`: Retrieved from `weatherApi` credentials
- **Output:** JSON containing weather forecasts, focusing on today's temperature.

### 3. Get Energy Pricing
- **Type:** HTTP Request
- **Description:** Obtains current regional energy pricing to analyze cost impact.
- **Endpoint:** `https://api.energyprices.com/v1/prices`
- **Query Parameters:**
  - `region`: `"NY"`
  - `date`: Current date in ISO format (YYYY-MM-DD)
- **Authentication:** Bearer Token via `energyPricingApi` credentials
- **Output:** JSON containing price per kWh.

### 4. Analyze Data & Generate Insights
- **Type:** Function Node
- **Description:** Processes data from the previous nodes to:
  - Extract and summarize energy usage.
  - Detect anomalies in consumption (usage > 1.5x average usage triggers an alert).
  - Integrate weather conditions for tailored recommendations.
  - Consider current energy prices for cost-saving advice.
  - Estimate carbon footprint assuming 0.7 kg CO₂ per kWh.
- **Outputs:** JSON containing:
  - `timestamp`: Data timestamp
  - `usage`: Current energy usage in kWh
  - `averageUsage`: Average daily energy usage in kWh
  - `alert`: Alert message if high consumption detected
  - `temperature`: Today's forecast temperature
  - `currentPrice`: Current energy price per kWh
  - `recommendations`: Array of energy-saving suggestions
  - `carbonFootprint`: Estimated carbon emissions in kg CO₂

### 5. Check For Alerts
- **Type:** IF Condition
- **Description:** Checks if any alert was generated based on anomalous energy usage.
- **Condition:** Trigger if `alert` field is not null or empty.

### 6. Send Alert Notification
- **Type:** Telegram
- **Description:** Sends immediate alert notifications to the homeowner through Telegram if an energy consumption anomaly is detected.
- **Credential:** Uses `telegramApi` credentials.
- **Message:** Alert text from the analysis node.

### 7. Log Energy Data
- **Type:** File
- **Description:** Appends analyzed daily energy data with insights to a local JSON file `energy_logs.json` for record-keeping and historical tracking.

### 8. Send Daily Report
- **Type:** Telegram
- **Description:** Sends a summary report every cycle including:
  - Total daily energy usage
  - Estimated daily carbon footprint
  - Practical recommendations tailored to current weather and energy pricing
- **Credential:** Uses `telegramApi` credentials.

---

## Data Flow Summary

1. **Smart meter data** is fetched first.
2. The trigger from the smart meter data simultaneously initiates both the **weather forecast** and **energy pricing** data retrieval.
3. Weather and energy pricing outputs flow into the **Analyze Data & Generate Insights** node.
4. Insights trigger three parallel paths:
   - **Alert checking**, which if active, sends a notification.
   - **Logging** the data to a file.
   - **Sending a daily report** via Telegram.

---

## Credentials Required

- **Smart Meter API:** API token for the smart meter provider.
- **Weather API:** API key for the weather service.
- **Energy Pricing API:** API token for energy pricing service.
- **Telegram API:** Bot token and channel for sending alerts and reports.

---

## Configuration Notes

- **Location:** The weather forecast is set for New York City using coordinates `40.7128,-74.0060`. Change `geocode` to reflect your location.
- **Region:** Energy pricing region is set to `"NY"`. Adjust as per your local region.
- **Alert Threshold:** Currently set to trigger when consumption exceeds 1.5 times the average usage.
- **Carbon Footprint Factor:** Assumes 0.7 kg CO₂ per kWh, which can be updated for more precise calculations.

---

## Execution & Activation

- Set all required credentials in your workflow environment.
- Activate the workflow to begin periodic data collection, analysis, alerting, and reporting.
- Ensure that the Telegram bot has permissions to send messages to the specified `homeowner` channel.

---

## Summary

This workflow automates smart home energy monitoring by combining real-time data inputs and intelligent analysis to provide actionable insights and alerts, promoting energy efficiency, cost savings, and environmental awareness.