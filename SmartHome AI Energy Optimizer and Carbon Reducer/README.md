# SmartHome AI Energy Optimizer and Carbon Reducer

This workflow leverages real-time and historical smart home energy consumption data to forecast future energy usage, analyze it for carbon footprint impact, and provide actionable recommendations to optimize energy consumption and reduce carbon emissions. Alerts with insights and suggestions are sent automatically via Telegram.

---

## Workflow Overview

The workflow consists of the following steps:

1. **Fetch Current Energy Usage**  
   Retrieves the current energy consumption from the SmartHome Energy API.

2. **Fetch Real-Time Energy Data**  
   Retrieves recent historical and real-time energy usage data to be used for forecasting.

3. **Forecast Energy Consumption**  
   Uses a simple moving average of the last 24 hours of historical usage to generate a 24-hour forecast of energy consumption.

4. **Analyze and Create Recommendations**  
   Evaluates the forecast and current usage data to:  
   - Identify peak usage during costly or high-carbon-emission periods.  
   - Calculate expected carbon emissions based on forecasted usage.  
   - Generate personalized energy-saving and carbon reduction recommendations.

5. **Send Alert via Telegram**  
   Sends a formatted message containing the energy usage forecast, estimated carbon footprint, and recommendations to a specified Telegram chat.

---

## Nodes Description

### 1. Start  
- Entry point of the workflow.

### 2. Fetch Current Energy Usage  
- HTTP GET request to the SmartHome Energy API endpoint `/v1/energy-usage/current`.  
- Retrieves the latest energy consumption reading.  
- Authenticated using HTTP Basic Auth credentials named `SmartHomeAPI Credentials`.

### 3. Fetch Real-Time Energy Data  
- HTTP GET request to `/v1/energy-usage/realtime` API endpoint.  
- Retrieves historical energy consumption data necessary for forecasting.  
- Also uses the `SmartHomeAPI Credentials` for authentication.

### 4. Forecast Energy Consumption  
- Function node performing a simple 24-hour forecast by calculating the moving average of hourly historical energy usage data.  
- Outputs a forecast array and average usage for subsequent analysis.

### 5. Analyze and Create Recommendations  
- Function node that:  
  - Receives forecasted and current usage data as inputs.  
  - Defines peak hours (17:00-21:00) and uses a carbon factor (0.45 kg CO₂ per kWh).  
  - Checks if current usage is significantly above average during peak hours to suggest usage shifting.  
  - Provides general advice like turning off unused devices.  
  - Calculates expected daily energy usage and corresponding carbon emissions.  
  - Compiles all findings and recommendations into a JSON output.

### 6. Send Alert via Telegram  
- Sends messages to a Telegram chat containing:  
  - Forecasted daily energy usage (kWh)  
  - Estimated carbon footprint (kg CO₂)  
  - List of energy and carbon reduction recommendations.  
- Requires Telegram API credentials (`Telegram API`).

---

## Credentials Required

- **SmartHomeAPI Credentials**  
  HTTP Basic Authentication credentials for accessing SmartHome Energy API endpoints.

- **Telegram API**  
  Telegram bot API credentials to send messages to users or groups.

---

## Configuration

- **Telegram Chat ID**  
  Modify the `chatId` parameter in the "Send Alert via Telegram" node to the Telegram chat or user ID where alerts will be sent.

- **API Endpoints**  
  The workflow uses the SmartHome Energy API base URL `https://api.smarthomeenergy.com`. Ensure your account has API access.

---

## How It Works

- The workflow starts and simultaneously requests the current energy usage and the historical real-time data.  
- The historical data is processed to forecast the next 24 hours of energy consumption using a simple average method.  
- Both the forecast and current usage feed into the analyzer, which calculates carbon footprints and generates tailored recommendations based on usage patterns and peak hours.  
- Finally, the summarized report and suggestions are sent via Telegram, enabling users to take actionable steps toward energy optimization.

---

## Notes

- The forecasting method employed is a simple moving average, suitable for straightforward usage patterns. For more advanced forecasting, consider integrating more sophisticated models or machine learning approaches.  
- Peak hours and carbon emission factors may require customization based on regional energy pricing and carbon intensity data.  
- Ensure all credentials are securely stored and permissions are correctly set to maintain privacy and security.

---

## License

This workflow is provided as-is without warranty. Customize as needed for your smart home energy optimization needs.