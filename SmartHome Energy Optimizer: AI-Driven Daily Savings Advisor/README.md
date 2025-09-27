# Next-Gen AI-Powered Home Energy Savings Advisor

This workflow automates the process of gathering home energy consumption data, analyzing it alongside weather forecasts and user habits, and then delivers personalized energy-saving recommendations daily. Additionally, it can control smart home devices based on those recommendations to optimize energy usage automatically.

---

## Workflow Overview

The workflow is designed in n8n and operates through a series of nodes for scheduled execution, data retrieval, analysis, recommendation generation, communication, and device control.

---

## Nodes & Functions

### 1. Daily Trigger  
- **Type:** Schedule Trigger  
- **Purpose:** Initiates the workflow every day at 6:00 AM automatically.  
- **Details:** Runs the workflow once daily to process fresh data and deliver updated recommendations.

### 2. Get Energy Consumption  
- **Type:** HTTP Request  
- **Purpose:** Fetches the latest energy consumption statistics from the smart meter API.  
- **API Endpoint:** `https://api.smartmeter.example.com/v1/energy-consumption`  
- **Authorization:** Uses a Bearer token from `smartMeterApiKey` in input JSON.  
- **Response Format:** JSON  
- **Data Retrieved:** Current and average energy usages necessary for generating recommendations.

### 3. Get Weather Forecast  
- **Type:** HTTP Request  
- **Purpose:** Retrieves a 5-day weather forecast to consider environmental conditions in recommendations.  
- **API Endpoint:** `https://api.weather.com/v3/wx/forecast/daily/5day`  
- **Parameters:**  
  - `geocode`: User's location from `userLocation` in input JSON  
  - `format`: JSON  
  - `language`: English (US)  
  - `apiKey`: Weather API key from `weatherApiKey` in input JSON  
- **Response Format:** JSON  
- **Data Retrieved:** Daily temperatures and weather insights for the forecast period.

### 4. Analyze User Habits  
- **Type:** Function  
- **Purpose:** Simulates gathering and analyzing user habits relevant to energy consumption, such as peak usage hours, temperature preferences, and device presence.  
- **Data Produced:**  
  - `peakUsageHours`: Hours when usage peaks (e.g., 6 PM - 8 PM)  
  - `preferredTempRange`: Preferred heating and cooling temperatures (68°F heating, 75°F cooling)  
  - `devices`: Indicates installed smart devices (AC, heater, smart lights)

### 5. Generate Recommendations  
- **Type:** Function  
- **Purpose:** Combines energy usage data, weather forecast, and user habits to create actionable energy-saving recommendations.  
- **Recommendation Logic:**  
  - Suggests reducing usage during peak hours if current consumption exceeds average.  
  - Advises using smart AC settings if forecasted high temperatures exceed preferred cooling range.  
  - Recommends smart heater scheduling if forecasted low temperatures are below preferred heating range.  
  - Encourages use of motion sensors or timers for lighting if smart lights are present.  
- **Output:** JSON array of recommendation strings.

### 6. Send Email Report  
- **Type:** Email Send  
- **Purpose:** Sends a daily email to the user containing personalized energy-saving recommendations.  
- **To:** Email address from `userEmail` input JSON.  
- **Subject:** "Daily Energy Savings Recommendations"  
- **Body Text:** A formatted list of the generated recommendations with a friendly closing message.

### 7. Control Smart AC Unit  
- **Type:** Smart Device Control  
- **Purpose:** Automatically adjusts the smart AC unit to eco mode at 75°F if the corresponding AC recommendation is included.  
- **Device ID:** From `smartDevices.acUnitId` in input JSON.  
- **Command:** `adjustTemperature` with parameters temperature = 75°F, mode = eco.  
- **Condition:** Executes only if "Consider using smart AC settings to maintain comfort while saving energy." is present in recommendations.

### 8. Control Smart Heater  
- **Type:** Smart Device Control  
- **Purpose:** Schedules the smart heater's operation based on optimized scheduling when relevant recommendation is present.  
- **Device ID:** From `smartDevices.heaterUnitId` in input JSON.  
- **Command:** `scheduleOnOff` with parameter `schedule: optimized`.  
- **Condition:** Executes only if "Use smart heater scheduling to optimize energy consumption." is present in recommendations.

---

## Workflow Connections

- The workflow starts from `Daily Trigger` node, triggering `Get Energy Consumption` and `Get Weather Forecast` nodes simultaneously.
- Both consumption and weather data nodes feed into `Analyze User Habits`, which processes user-specific behavior.
- The analyzed user habits plus retrieved data go into `Generate Recommendations` for generating actionable tips.
- `Generate Recommendations` outputs feed into three nodes that:
  - Send email with suggestions to the user.
  - Potentially control the smart AC unit.
  - Potentially control the smart heater.

---

## How to Use

1. **Set API Keys and User Data:**  
   Provide the following in the workflow execution context or as credentials:  
   - `smartMeterApiKey` — Authentication token for the smart meter API  
   - `weatherApiKey` — Weather service API key  
   - `userLocation` — Geographical coordinates for weather data  
   - `userEmail` — Email to send recommendations  
   - `smartDevices` — Object containing smart device IDs: `acUnitId` and `heaterUnitId`

2. **Schedule Activation:**  
   Enable the workflow to run daily at 6:00 AM or adjust the schedule in the `Daily Trigger` node.

3. **Monitor and Maintain:**  
   Ensure API endpoints and smart device integrations remain operational and credentials are up-to-date.

---

## Benefits

- Automated, daily personalized insights on how to reduce home energy usage.  
- Combines smart meter data, weather forecasts, and user preferences for tailored advice.  
- Direct communication via email keeps users informed and engaged.  
- Smart device controls can proactively optimize energy usage without manual intervention.  

---

## Potential Enhancements

- Expand user habits analysis with real historical data for more precise recommendations.  
- Integrate additional device controls, such as smart lighting or appliances.  
- Include cost-saving estimates alongside energy recommendations.  
- Use AI/ML for predictive analytics and adaptive learning of user preferences.

---

Harness the power of AI, IoT, and automation to maximize your home’s energy efficiency and savings with this comprehensive daily advisory workflow.