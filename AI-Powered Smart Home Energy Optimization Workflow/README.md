# AI-Driven Smart Home Energy Management and Cost Optimization System

This workflow optimizes smart home energy consumption by combining real-time smart meter data, weather conditions, and IoT device status to provide actionable insights, user alerts, and automatic device adjustments. It leverages AI-driven logic to reduce energy costs and maximize renewable energy usage.

---

## Workflow Overview

The workflow consists of the following main steps:

1. **Get Smart Meter Data**  
   Retrieves real-time energy consumption data from the smart meter installed in the home.

2. **Get Weather Data**  
   Fetches current weather information using the OpenWeather API based on the home's geolocation (latitude and longitude).

3. **Get IoT Devices Status**  
   Collects the current status of connected IoT devices in the home.

4. **Analyze Energy Usage**  
   Processes the aggregated data to:
   - Determine if the current time falls within peak energy usage hours.
   - Assess renewable energy availability based on weather conditions.
   - Generate energy usage recommendations.
   - Generate critical usage alerts if necessary.

5. **Send User Alerts and Recommendations**  
   Sends combined alerts and recommendations directly to the user to encourage optimal energy consumption behavior.

6. **Adjust IoT Devices**  
   Automatically adjusts the power settings of IoT devices based on peak hour status and renewable energy availability:
   - Reduces power during peak hours to save costs.
   - Increases power usage when renewable energy is abundant.
   - Maintains current settings when neither condition applies.

---

## Detailed Node Descriptions

### 1. Get Smart Meter Data
- **Type:** Smart Meter Node
- **Operation:** `getData` from the `smartMeter` resource.
- **Purpose:** Provides current energy consumption metrics to assess usage patterns.

### 2. Get Weather Data
- **Type:** HTTP Request Node
- **API:** OpenWeather API `/data/2.5/weather`
- **Parameters:**  
  - Latitude and longitude dynamically extracted from the input data.  
  - API key provided via credentials.  
  - Units set to metric system.
- **Purpose:** Determines weather conditions to infer renewable energy availability (i.e., sunny or cloudy).

### 3. Get IoT Devices Status
- **Type:** IoT Node
- **Operation:** `getData` from the `iotDevicesStatus` resource.
- **Purpose:** Retrieves statuses of smart home devices to monitor operational conditions.

### 4. Analyze Energy Usage
- **Type:** Function Node
- **Logic:**
  - Retrieves energy usage, weather, and IoT device data.
  - Checks if current hour is within defined peak hours (17-21).
  - Determines renewable energy availability if weather is ‘Clear’ or ‘Clouds’.
  - Provides recommendations to reduce or maximize usage accordingly.
  - Generates alerts if energy usage exceeds a set threshold (10 units).
- **Outputs:**
  - Current energy usage.
  - Weather data.
  - IoT device status.
  - Peak hour flag.
  - Renewable energy availability flag.
  - Recommendations array.
  - Alerts array.

### 5. Send User Alerts and Recommendations
- **Type:** Notifications Node
- **Data:** Sends combined alerts and recommendations as a single notification message to the user.
- **Purpose:** Keeps the user informed to promote better energy management habits.

### 6. Adjust IoT Devices
- **Type:** IoT Node
- **Operation:** `controlDevice` on `iotDevices` resource.
- **Control Logic:**  
  - During peak hours: Reduce device power consumption.  
  - When renewable energy is available: Increase device power utilization to maximize green energy use.  
  - Otherwise: Maintain current settings.
- **Purpose:** Automates energy cost optimization by regulating IoT devices.

---

## Connections Flow

- **Data Fetching:**  
  - Smart Meter Data, Weather Data, and IoT Devices Status nodes feed their outputs into the Analyze Energy Usage node.
  
- **Decision & Action:**  
  - Analyze Energy Usage outputs trigger both the Send User Alerts and Recommendations node and the Adjust IoT Devices node.

---

## Credentials

- **OpenWeather API:**  
  A valid API key is required and configured via the `openWeatherApi` credential to access weather data.

---

## Summary

This workflow provides a comprehensive AI-driven solution to manage and optimize home energy use by integrating smart meter data, weather insights, and IoT device control, enhancing cost savings and sustainability for smart home users.