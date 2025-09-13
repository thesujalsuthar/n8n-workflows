# AI-Powered Personalized Renewable Energy Usage Optimizer and Cost Savings Forecast

## Overview

This n8n workflow is designed to optimize renewable energy usage for individual users by analyzing past energy consumption, weather and solar forecasts, and current energy prices. It leverages AI-driven optimization techniques to create an energy usage schedule that maximizes self-consumption from solar power, reduces grid dependency, and forecasts potential cost savings. The optimization results are stored for future reference and a summary output is prepared for easy consumption.

---

## Workflow Components

### 1. Webhook Trigger
- **Node Name:** Webhook Trigger
- **Function:** Receives POST requests at the path `/optimize-energy-usage` to initiate the workflow.
- **Details:** Accepts input JSON which should include user location (latitude and longitude).

### 2. Get Energy Consumption
- **Node Name:** Get Energy Consumption
- **Type:** Spreadsheet File
- **Function:** Retrieves past energy consumption data uploaded by the user.
- **Credentials:** Requires access to a spreadsheet file credential which contains user consumption data.

### 3. Fetch Weather & Solar Forecast
- **Node Name:** Fetch Weather & Solar Forecast
- **Type:** HTTP Request
- **Function:** Calls OpenWeather's One Call API to get weather and solar forecast data for the user's geographic coordinates.
- **Parameters:**
  - Latitude and longitude from webhook JSON input
  - Excludes minutely and alerts
  - Metric units
  - Requires OpenWeather API key credentials

### 4. Fetch Energy Prices
- **Node Name:** Fetch Energy Prices
- **Type:** HTTP Request
- **Function:** Pulls current energy prices from an external API to incorporate cost factors into the optimization.
- **Authentication:** Uses API key in `Authorization` header.
- **Note:** Integration of fetched prices is currently a placeholder in the workflow.

### 5. Integrate Energy Prices (Placeholder)
- **Node Name:** Integrate Energy Prices (Placeholder)
- **Type:** Function
- **Function:** Placeholder node to merge energy prices data into the optimization logic.
- **Current Status:** Passes input as-is.

### 6. Analyze Consumption & Solar Data
- **Node Name:** Analyze Consumption & Solar Data
- **Type:** Function
- **Function:** Processes users' past energy consumption and aligns it with weather forecast data to identify usage patterns and predicts solar energy generation potential.
- **Logic Details:**
  - Iterates through hourly weather data
  - Estimates solar generation potential using cloud cover and solar elevation proxies
  - Creates an array of predicted consumption and solar potential per hour

### 7. Optimize Energy Usage & Forecast Savings
- **Node Name:** Optimize Energy Usage & Forecast Savings
- **Type:** Function
- **Function:** Performs AI-like optimization using simplified heuristics to create an optimized schedule that maximizes solar energy usage and forecasts monetary savings.
- **Optimization Logic:**
  - Uses solar energy when solar potential is above a threshold (0.3)
  - Assigns hypothetical cost savings per kWh: $0.15 when using solar, $0.05 when from grid
  - Calculates cost savings per hour and totals them

### 8. Store Optimization Result
- **Node Name:** Store Optimization Result
- **Type:** MongoDB
- **Function:** Saves the final optimized schedule and forecasted savings into a MongoDB collection `users_energy_data`.
- **Credentials:** Requires MongoDB credentials.

### 9. Prepare Output Summary
- **Node Name:** Prepare Output Summary
- **Type:** Function
- **Function:** Creates a concise JSON output message with:
  - Confirmation message
  - Total forecasted cost savings (USD)
  - Sample of the optimized schedule (first 5 hours)

---

## Data Flow Summary

1. Workflow is triggered via the webhook with user location data.
2. User energy consumption data is fetched from a spreadsheet file.
3. Weather and solar forecast data is retrieved from OpenWeather API.
4. Current energy prices are fetched from an external service (currently placeholder).
5. Energy prices integrated into the data (currently mocked).
6. Consumption and solar data combined and analyzed for patterns.
7. AI-driven optimization generates a usage schedule and forecasts cost savings.
8. Results are stored in MongoDB.
9. Summary response is prepared and returned.

---

## Requirements and Credentials

- **OpenWeather API Key:** For retrieving weather and solar forecast data.
- **Spreadsheet File Credential:** Access to user's historical energy consumption data.
- **MongoDB Credential:** For storing optimization outputs.
- **Energy Prices API Key:** For fetching current energy prices and forecasts.

---

## Deployment and Usage

1. Import this workflow into your n8n instance.
2. Configure credentials for OpenWeather, MongoDB, spreadsheet file, and energy prices API.
3. Ensure the spreadsheet file contains historical user energy consumption data.
4. Trigger the workflow by sending a POST request to the webhook URL `/optimize-energy-usage` with JSON payload including `latitude` and `longitude`.
5. The workflow will process data, optimize the energy usage schedule, forecast savings, and store results.
6. The response will contain a summary message with estimated savings and optimized schedule preview.

---

## Notes

- The current AI optimization uses a heuristic model and solar potential estimation is simplified.
- Integration with energy prices is a placeholder for future enhancement.
- Consumption data is expected in hourly granularity and pre-uploaded.
- Cloud cover and solar elevation calculations are proxies; more precise solar modeling can improve optimization accuracy.

---

## License

This workflow is provided as-is for demonstration and educational purposes. Modify and extend as needed for production environments.