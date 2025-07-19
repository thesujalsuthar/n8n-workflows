# Smart Home Energy Usage Optimizer and Alert System

## Overview  
This workflow collects and analyzes real-time energy consumption data from a smart home, combines this with weather forecasts and current utility rates, and provides optimization tips along with alert notifications for unusual energy usage. It helps users monitor and optimize their home energy usage proactively.

---

## Workflow Nodes Description  

### 1. Start  
- Entry point of the workflow. Triggers the subsequent nodes to fetch data.

---

### 2. Get Real-time Energy Data  
- **Type:** HTTP Request  
- **Purpose:** Fetches real-time energy consumption data from the `SmartHomeEnergyAPI`.  
- **Output:** Returns all available real-time energy data including current usage and timestamp.

---

### 3. Analyze Consumption Patterns  
- **Type:** Function  
- **Purpose:** Analyzes real-time energy data to detect unusual consumption by comparing current usage against a threshold derived from the previous average usage (e.g., 1.5 times the previous average).  
- **Logic:**  
  - Retrieves current usage from energy data.  
  - Compares against `thresholdMultiplier` * `previousAverage` (example default: 1.5 * 500 watts).  
  - Flags unusual consumption if exceeded.  

- **Output:** Contains `currentUsage`, `unusualConsumption` (boolean), and `timestamp`.

---

### 4. Check Unusual Consumption  
- **Type:** If (Conditional Node)  
- **Purpose:** Determines if there is an unusual consumption event.  
- **Condition:** Passes only if `unusualConsumption` is `true`.  

---

### 5. Send Alert Email  
- **Type:** Email Send  
- **Purpose:** Sends an alert to the user if unusual energy consumption is detected.  
- **Email Details:**  
  - From: `alerts@smarthome.com`  
  - To: `user@example.com`  
  - Subject: "Alert: Unusual Energy Consumption Detected"  
  - Body: Includes the timestamp and current usage in watts, both in plain text and HTML format.  

---

### 6. Get Weather Forecast  
- **Type:** HTTP Request  
- **Purpose:** Fetches weather forecast data for a fixed location (`latitude=1.2345, longitude=6.7890`) from `WeatherAPI`.  
- **Output:** Complete weather forecast data including temperature.

---

### 7. Generate Optimization Tips  
- **Type:** Function  
- **Purpose:** Provides energy usage optimization tips based on forecasted weather conditions.  
- **Logic:**  
  - If forecasted max temperature > 25°C: Advises reducing air conditioning usage during peak hours.  
  - If forecasted min temperature < 10°C: Suggests optimizing heating schedule.  
  - Otherwise, indicates typical energy usage based on weather.  

- **Output:** An array `optimizationTips` with recommendation messages.

---

### 8. Get Utility Rates  
- **Type:** HTTP Request  
- **Purpose:** Retrieves current electricity rates from `UtilityRateAPI`.  
- **Output:** Includes current rate details.

---

### 9. Analyze Utility Rate Changes  
- **Type:** Function  
- **Purpose:** Monitors electricity rates and generates messages advising users when rates are high.  
- **Logic:**  
  - Compares current rate against a high rate threshold (example set at $0.20 per kWh).  
  - Sends alert if rates are above threshold advising reduction in usage.  
  - Otherwise, informs that rates are normal.

- **Output:** `rateMessages` array containing rate advice.

---

### 10. Compile Suggestions  
- **Type:** Function  
- **Purpose:** Compiles optimization tips and rate advice into one summarized message for potential further use or display.  
- **Output:** A combined message including:  
  - "Optimization Suggestions:" label  
  - Weather-based optimization tips  
  - Utility rate advice messages  

---

## Workflow Connections  

- **Start** triggers the following nodes in parallel:  
  - Get Real-time Energy Data  
  - Get Weather Forecast  
  - Get Utility Rates  

- **Get Real-time Energy Data** → Analyze Consumption Patterns → Check Unusual Consumption:  
  - If unusual consumption detected → Send Alert Email  

- **Get Weather Forecast** → Generate Optimization Tips  

- **Get Utility Rates** → Analyze Utility Rate Changes  

- Optimization tips and rate messages are compiled in `Compile Suggestions` (node present but not connected in current workflow version).

---

## Customization Points  

- **Threshold for unusual consumption:** Adjust `thresholdMultiplier` and `previousAverage` values in "Analyze Consumption Patterns" node.  
- **Location for weather forecast:** Update coordinates in "Get Weather Forecast" node parameters.  
- **Utility rate high threshold:** Modify `highRateThreshold` in "Analyze Utility Rate Changes" node.  
- **Email recipient and sender:** Customize in "Send Alert Email" node settings.

---

## Summary  
This Smart Home Energy Usage Optimizer and Alert System offers a comprehensive mechanism to:  
- Continuously monitor real-time energy consumption.  
- Detect unusual usage patterns and notify users immediately.  
- Provide actionable optimization tips based on local weather conditions.  
- Alert users about electricity rate changes to enable cost-saving decisions.  

By integrating multiple data sources and triggering notifications, this workflow empowers users to manage and optimize their home energy use effectively.