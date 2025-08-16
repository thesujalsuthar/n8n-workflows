# Personalized Eco-Friendly Smart Home Energy Optimization

This workflow automates the optimization of your smart home energy consumption by integrating real-time energy usage data with current weather information. It provides personalized energy-saving tips, automates smart device settings accordingly, and sends weekly energy reports via email.

---

## Workflow Overview

### 1. Trigger - Energy Check  
- **Type:** Interval Trigger  
- **Interval:** Every 5 minutes (300 seconds)  
- **Purpose:** Initiates the energy data retrieval and analysis cycle to continuously monitor energy usage.

### 2. Get Real-Time Energy Data  
- **Type:** HTTP Request  
- **API:** Smart Home API (`https://api.smarthome.example.com/devices/energy-consumption`)  
- **Authentication:** Bearer Token Authorization using `Smart Home API Credentials`.  
- **Purpose:** Fetches the current energy consumption data across smart home devices.

### 3. Get Current Weather  
- **Type:** HTTP Request  
- **API:** Weather API (`https://api.weather.example.com/current`) with dynamic latitude & longitude parameters.  
- **Authentication:** API key passed via header (`x-api-key`) using `Weather API Credentials`.  
- **Purpose:** Retrieves current weather conditions to factor environmental data into the energy optimization.

### 4. Analyze Usage and Suggest Tips  
- **Type:** Function Node  
- **Logic:**  
  - Reads total energy consumption (in Watts) and outside temperature (°C).  
  - Generates personalized energy-saving tips based on:  
    - High energy usage (>5000W)  
    - Temperature thresholds (>25°C for cooling, <18°C for heating)  
    - General recommendations such as using LED lights and unplugging devices when idle.  
- **Output:** Adds an array of energy tips and current temperature data to the workflow payload.

### 5. Automate Smart Devices  
- **Type:** HTTP Request (POST)  
- **API:** Smart Home Device Control (`https://api.smarthome.example.com/devices/control`)  
- **Authentication:** Bearer Token Authorization as in step 2.  
- **Body Parameters:**  
  - `deviceId`: `thermostat001`  
  - `action`: `optimizeSettings`  
- **Purpose:** Sends commands to smart devices (e.g., thermostat) to automatically adjust settings based on analyzed data and tips.

### 6. Weekly Report Trigger  
- **Type:** Interval Trigger  
- **Interval:** Every 7 days (604800 seconds)  
- **Purpose:** Initiates the weekly reporting process to summarize energy consumption and tips for the user.

### 7. Generate Report Content  
- **Type:** Function Node  
- **Logic:** Constructs an email-friendly energy report including:  
  - Total energy consumption (Watts)  
  - Outside temperature (°C)  
  - Personalized energy-saving tips formatted as a list  
- **Output:** Produces email subject and body text.

### 8. Send Energy Report Email  
- **Type:** Email Send Node  
- **From:** `noreply@smarthome.com`  
- **To:** User email address provided in workflow data  
- **Subject & Text:** Dynamically assigned from previous node  
- **Purpose:** Sends the weekly energy optimization report to the user.

### 9. Start  
- **Type:** Start Node  
- **Purpose:** Entry point to enable workflow execution.

---

## Credentials Required

- **Smart Home API Credentials**  
  - API Key for accessing energy consumption and device control endpoints.

- **Weather API Credentials**  
  - API Key for querying current weather data.

---

## Installation and Setup

1. Import this workflow into your n8n instance.
2. Configure the **Smart Home API Credentials** with your API key.
3. Configure the **Weather API Credentials** with your weather API key.
4. Adjust the email node's `toEmail` field dynamically or set a static recipient as needed.
5. Activate the workflow.

---

## How It Works

- The 5-minute interval trigger keeps your home's energy consumption monitored in near real-time.
- Energy consumption data and weather context are combined to generate actionable energy-saving advice.
- Smart home devices, specifically the thermostat, are adjusted automatically to optimize energy use.
- A weekly summary report email provides insights and tips, helping users maintain eco-friendly living practices.

---

## Customization

- Modify the `Generate Tips` function to tailor advice to your devices and preferences.
- Adjust intervals for data checks and report frequency as per requirements.
- Expand device controls in the `Automate Smart Devices` node to other smart devices.

---

## Notes

- Ensure that latitude and longitude values are dynamically provided or replaced for accurate weather data.
- API URLs and device IDs should match your actual smart home and weather service endpoints.
- Make sure email sending credentials and host configuration in n8n are properly set for successful delivery.

---

Stay green and optimize your smart home energy efficiently with this workflow!